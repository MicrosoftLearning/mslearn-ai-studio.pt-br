---
lab:
  title: Criar um aplicativo de chat de IA generativa
  description: Saiba como usar o SDK da Fábrica de IA do Azure para criar um aplicativo que se conecta ao seu projeto e chat com um modelo de linguagem.
---

# Criar um aplicativo de chat de IA generativa

Neste exercício, você usa o SDK do Python da Fábrica de IA do Azure para criar um aplicativo de chat simples que se conecta a um projeto e chat com um modelo de linguagem.

> **Observação**: Este exercício é baseado em software de SDK pré-lançamento, que pode estar sujeito a alterações. Quando necessário, usamos versões específicas de pacotes que podem não refletir as versões mais recentes disponíveis. Você pode experimentar algum comportamento inesperado, avisos ou erros.

Embora este exercício seja baseado no SDK do Python da Fábrica de IA do Azure, você pode desenvolver aplicativos de chat de IA usando vários SDKs específicos de linguagem; incluindo:

- [Projetos de IA do Azure para Python](https://pypi.org/project/azure-ai-projects)
- [Projetos de IA do Azure para Microsoft .NET](https://www.nuget.org/packages/Azure.AI.Projects)
- [Projetos de IA do Azure para JavaScript](https://www.npmjs.com/package/@azure/ai-projects)

Este exercício leva aproximadamente **40** minutos.

## Implantar um modelo em um projeto da Fábrica de IA do Azure

Vamos começar implantando um modelo em um projeto da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir (feche o painel **Ajuda** se estiver aberto):

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. Na home page, na seção **Explorar modelos e recursos**, pesquise pelo modelo `gpt-4o`, que usaremos em nosso projeto.
1. Nos resultados da pesquisa, selecione o modelo **gpt-4o** para ver os detalhes e, na parte superior da página do modelo, clique em **Usar este modelo**.
1. Quando solicitado a criar um projeto, insira um nome válido para o projeto e expanda **Opções avançadas**.
1. Selecione **Personalizar** e especifique as seguintes configurações para o seu projeto:
    - **Recurso da Fábrica de IA do Azure**: *um nome válido para o recurso da Fábrica de IA do Azure*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Região**: *Selecione qualquer **Local compatível com os Serviços de IA***\*

    > \* Alguns recursos da IA do Azure são restritos por cotas de modelo regional. Caso um limite de cota seja excedido posteriormente no exercício, é possível que você precise criar outro recurso em uma região diferente.

1. Clique em **Criar** e aguarde a criação do projeto. Se solicitado, implante o modelo gpt-4o usando o tipo de implantação **Global padrão** e personalize os detalhes da implantação para definir um **limite de tokens por minuto** de 50 mil (ou o máximo disponível, se for inferior a 50 mil).

    > **Observação**: A redução do TPM ajuda a evitar o uso excessivo da cota disponível na assinatura que você está usando. 50.000 TPM são suficientes para os dados usados neste exercício. Se a sua cota disponível for menor do que isso, você poderá concluir o exercício, mas poderá ocorrer erros se o limite de taxa for excedido.

1. Quando o projeto for criado, o playground de chat abrirá automaticamente para que você possa testar o modelo:
1. No painel **Configuração**, anote o nome da implantação do modelo; que será **gpt-4o**. Você pode confirmar isso visualizando a implantação na página **Modelos e pontos de extremidade** (basta abrir essa página no painel de navegação à esquerda).
1. No painel de navegação à esquerda, selecione **Visão geral** para ver a página principal do projeto, que será assim:

    ![Captura de tela de uma página de visão geral do projeto da Fábrica de IA do Azure.](./media/ai-foundry-project.png)

## Criar um aplicativo cliente para conversar com o modelo

Agora que você implantou um modelo, pode usar a Fábrica de IA do Azure e os SDKs do Azure OpenAI para desenvolver um aplicativo que converse com ele.

### Preparar a configuração de aplicativo

1. No Portal da Fábrica de IA do Azure, visualize a página **Visão geral** do seu projeto.
1. Na área **Pontos de extremidade e chaves**, verifique se a biblioteca da **Fábrica de IA do Azure** está selecionada e visualize o **ponto de extremidade do projeto da Fábrica de IA do Azure**. Você usará esse ponto de extremidade para se conectar ao projeto e ao modelo em um aplicativo cliente.

    > **Observação**: Você também pode usar o ponto de extremidade do Azure OpenAI.

1. Abra uma nova guia do navegador (mantendo o portal da Fábrica de IA do Azure aberto na guia existente). Em seguida, na nova guia, navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`; efetue login com suas credenciais do Azure, se solicitado.

    Feche todas as notificações de boas-vindas para ver a home page do portal do Azure.

1. Use o botão **[\>_]** à direita da barra de pesquisa na parte superior da página para criar um Cloud Shell no portal do Azure selecionando um ambiente do ***PowerShell*** sem armazenamento em sua assinatura.

    O Cloud Shell fornece uma interface de linha de comando em um painel na parte inferior do portal do Azure. Você pode redimensionar ou maximizar esse painel para facilitar o trabalho.

    > **Observação**: se você já criou um Cloud Shell que usa um ambiente *Bash*, alterne-o para o ***PowerShell***.

1. Na barra de ferramentas do Cloud Shell, no menu **Configurações**, selecione **Ir para a versão clássica** (isso é necessário para usar o editor de código).

    **<font color="red">Verifique se você mudou para a versão clássica do Cloud Shell antes de continuar.</font>**

1. No painel do Cloud Shell, insira os seguintes comandos para clonar o repositório GitHub que contém os arquivos de código para este exercício (digite o comando ou copie-o para a área de transferência e clique com o botão direito do mouse na linha de comando e cole como texto sem formatação):

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Dica**: ao colar comandos no Cloud Shell, a saída poderá ocupar uma grande quantidade do buffer da tela. Você pode limpar a tela digitando o comando `cls` para facilitar o foco em cada tarefa.

1. Após a clonagem do repositório, navegue até a pasta contendo os arquivos de código do aplicativo de chat e exiba-os:

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
   ls -a -l
    ```

    A pasta contém um arquivo de código, bem como um arquivo de configuração para as configurações do aplicativo e um arquivo que define os requisitos de runtime e pacote do projeto.

1. No painel de linha de comando do Cloud Shell, digite o seguinte comando para instalar as bibliotecas que você usará:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-identity azure-ai-projects openai
    ```
1. Digite o seguinte comando para editar o arquivo de configuração que foi fornecido:

    ```
   code .env
    ```

    O arquivo é aberto em um editor de código.

1. No arquivo de código, substitua o espaço reservado **your_project_endpoint** pelo **ponto de extremidade do projeto da Fábrica de IA do Azure** para seu projeto (copiado da página **Visão geral** no portal da Fábrica de IA do Azure); e substitua o espaço reservado **your_model_deployment** pelo nome da implantação do modelo gpt-4.
1. Depois de substituir os espaços reservados, use o comando **CTRL+S** ou **botão direito do mouse > Salvar** para salvar as suas alterações e, em seguida, use o comando **CTRL+Q** ou **botão direito do mouse > Sair** para fechar o editor de código, mantendo a linha de comando do Cloud Shell aberta.

### Escrever código para se conectar ao seu projeto e conversar com seu modelo

> **Dica**: ao adicionar código, certifique-se de manter o recuo correto.

1. Digite o seguinte comando para editar o arquivo de código que foi fornecido:

    ```
   code chat-app.py
    ```

1. No arquivo de código, observe as instruções existentes que foram adicionadas na parte superior do arquivo para importar os namespaces do SDK necessários. Em seguida, no comentário **Adicionar referências**, adicione o seguinte código para referenciar os namespaces nas bibliotecas que você instalou anteriormente:

    ```python
   # Add references
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
    ```

1. Na função **main**, no comentário **Get configuration settings**, observe que o código carrega a cadeia de conexão do projeto e os valores do nome de implantação do modelo que você definiu no arquivo de configuração.
1. Localize o comentário **Inicializar o cliente do projeto** e adicione o seguinte código para se conectar à Fábrica de IA do Azure:

    > **Dica**: Tenha cuidado para manter o nível de recuo correto no seu código.

    ```python
   # Initialize the project client
   project_client = AIProjectClient(            
            credential=DefaultAzureCredential(
                exclude_environment_credential=True,
                exclude_managed_identity_credential=True
            ),
            endpoint=project_endpoint,
        )
    ```

1. No comentário **Get a chat client**, adicione o seguinte código para criar um objeto cliente para conversar com um modelo:

    ```python
   # Get a chat client
   openai_client = project_client.get_openai_client(api_version="2024-10-21")
    ```

1. Localize o comentário **Initialize prompt with system message** e adicione o código a seguir para inicializar uma coleção de mensagens com um prompt do sistema.

    ```python
   # Initialize prompt with system message
   prompt = [
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
        ]
    ```

1. Observe que o código inclui um loop para permitir que o usuário insira um prompt até digitar "quit". Em seguida, na seção de loop, localize o comentário **Get a chat completion** e adicione o seguinte código para adicionar a entrada de usuário ao prompt, obter a conclusão do seu modelo e adicionar a conclusão ao prompt (para que você mantenha o histórico de chat em iterações futuras):

    ```python
   # Get a chat completion
   prompt.append({"role": "user", "content": input_text})
   response = openai_client.chat.completions.create(
            model=model_deployment,
            messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append({"role": "assistant", "content": completion})
    ```

1. Use o comando **CTRL+S** para salvar suas alterações no arquivo de código.

### Execute o aplicativo e entre no Azure.

1. No painel de linha de comando do Cloud Shell, digite o seguinte comando para entrar no Azure.

    ```
   az login
    ```

    **<font color="red">Você deve entrar no Azure, mesmo que a sessão do Cloud Shell já esteja autenticada.</font>**

    > **Observação**: na maioria dos cenários, apenas usar *az login* será suficiente. No entanto, se você tiver assinaturas em vários locatários, talvez seja necessário especificar o locatário usando o parâmetro *--tenant* . Consulte [Entrar no Azure interativamente usando a CLI do Azure](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively) para obter detalhes.
    
1. Quando solicitado, siga as instruções para abrir a página de entrada em uma nova guia e insira o código de autenticação fornecido e suas credenciais do Azure. Em seguida, conclua o processo de entrada na linha de comando, selecionando a assinatura que contém o hub da Fábrica de IA do Azure, se solicitado.
1. Depois de entrar, insira o seguinte comando para executar o aplicativo:

    ```
   python chat-app.py
    ```

1. Quando solicitado, insira uma pergunta, como `What is the fastest animal on Earth?` e analise a resposta do seu modelo de IA generativa.
1. Tente algumas perguntas de acompanhamento, como `Where can I see one?` ou `Are they endangered?`. A conversa deve continuar, usando o histórico de bate-papo como contexto para cada iteração.
1. Quando terminar, digite `quit` para sair do programa.

> **Dica**: se o aplicativo falhar porque o limite de taxa foi excedido, aguarde alguns segundos e tente novamente. Se não houver cota suficiente disponível em sua assinatura, o modelo talvez não consiga responder.

## Resumo

Neste exercício, você usou o SDK da Fábrica de IA do Azure para criar um aplicativo cliente para um modelo de IA generativa que você implantou em um projeto da Fábrica de IA do Azure.

## Limpar

Se tiver terminado de explorar o portal do Azure AI Foundry, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Abra o [portal do Azure](https://portal.azure.com) e exiba o conteúdo do grupo de recursos em que você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.
