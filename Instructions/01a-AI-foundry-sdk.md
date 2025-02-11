---
lab:
  title: Criar um aplicativo de chat com o SDK da Fábrica de IA do Azure
---

# Criar um aplicativo de chat com o SDK da Fábrica de IA do Azure

Neste exercício, você usará o SDK da Fábrica de IA do Azure para criar um aplicativo de chat simples.

Este exercício levará aproximadamente **20** minutos.

## Criar um projeto do Azure AI Foundry

Vamos começar criando um projeto da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir:

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. Na home page, selecione **+Criar projeto**.
1. No assistente **Criar um projeto**, insira um nome de projeto adequado (por exemplo, `my-ai-project`) e revise os recursos do Azure que serão criados automaticamente para dar suporte ao seu projeto.
1. Selecione **Personalizar** e especifique as seguintes configurações para o hub:
    - **Nome do hub**: *um nome exclusivo – por exemplo `my-ai-hub`*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *crie um novo grupo de recursos com um nome exclusivo (por exemplo, `my-ai-resources`) ou escolha um já criado*
    - **Localização**: faça uma escolha aleatória de uma das regiões na lista a seguir \*:
        - Leste dos EUA
        - Leste dos EUA 2
        - Centro-Norte dos EUA
        - Centro-Sul dos Estados Unidos
        - Suécia Central
        - Oeste dos EUA
        - Oeste dos EUA 3
    - **Conectar os Serviços de IA do Azure ou o OpenAI do Azure**: *crie um novo recurso de Serviços de IA com um nome apropriado (por exemplo, `my-ai-services`) ou use um existente*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* As cotas de modelo são restringidas no nível do locatário por cotas regionais. Escolher uma região aleatória ajuda a distribuir a disponibilidade da cota quando vários usuários estão trabalhando no mesmo locatário. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente.

1. Clique em **Avançar** e revise a configuração. Em seguida, clique em **Criar** e aguarde a conclusão do processo.
1. Quando o projeto for criado, feche todas as dicas exibidas e examine a página do projeto no portal da Fábrica de IA do Azure, que será semelhante à imagem a seguir:

    ![Captura de tela dos detalhes do projeto de IA do Azure no portal da Fábrica de IA do Azure.](./media/ai-foundry-project.png)

## Implantar um modelo de IA generativa

Agora você já pode implantar um modelo de linguagem de IA generativa para dar suporte ao aplicativo de chat. Neste exemplo, você usará o modelo Microsoft Phi-4; mas os princípios são os mesmos para qualquer modelo.

1. No painel à esquerda do seu projeto, na seção **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Na página **Modelos + pontos de extremidade,** na guia **Implantações de modelo**, no menu **+ Implantar modelo**, selecione **Implantar modelo de base**.
1. Procure o modelo **Phi-4** na lista e, em seguida, selecione-o e confirme-o.
1. Selecione a opção de implantação **API sem servidor com a Segurança de Conteúdo de IA do Azure**.
1. Crie uma nova implantação do modelo com as seguintes configurações selecionando **Personalizar** nos detalhes de implantação:
    - **Nome da implantação**: *um nome exclusivo para a implantação do modelo – por exemplo, `phi-4-model` (lembre-se do nome atribuído, você precisará dele mais tarde*)
    - **Filtro de conteúdo**: Ativado

## Criar um aplicativo cliente para conversar com o modelo

Agora que você implantou um modelo, pode usar o SDK da Fábrica de IA do Azure para desenvolver um aplicativo que se comunica com ele.

### Preparar a configuração do aplicativo

1. Acesse a página **Visão geral** do projeto no Portal da Fábrica de IA do Azure.
1. Na área **Detalhes do projeto**, observe a **Cadeia de conexão do projeto**. Você usará essa cadeia de conexão para se conectar ao seu projeto em um aplicativo cliente.
1. Abra uma nova guia do navegador (mantendo o portal da Fábrica de IA do Azure aberto na guia existente). Em uma guia, abra o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` e, se solicitado, entre usando suas credenciais do Azure.
1. Use o botão **[\>_]** à direita da barra de pesquisa na parte superior da página para criar um Cloud Shell no portal do Azure selecionando um ambiente do ***PowerShell***. O Cloud Shell fornece uma interface de linha de comando em um painel na parte inferior do portal do Azure.

    > **Observação**: se você já criou um Cloud Shell que usa um ambiente *Bash*, alterne-o para o ***PowerShell***.

1. Na barra de ferramentas do Cloud Shell, no menu **Configurações**, selecione **Ir para a versão clássica** (isso é necessário para usar o editor de código).

1. No painel do PowerShell, insira os seguintes comandos para clonar o repositório GitHub para este exercício:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

1. Depois que o repositório for clonado, navegue até a pasta **mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python**:

    ```
    cd mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python
    ```

1. No painel de linha de comando do Cloud Shell, insira o seguinte comando para instalar as bibliotecas Python que serão usadas, sendo elas:
    - **python-dotenv** : usada para carregar configurações de um arquivo de configuração do aplicativo.
    - **azure-identity**: usada para autenticar com credenciais do Entra ID.
    - **azure-ai-projects**: usada para trabalhar com um projeto da Fábrica de IA do Azure.
    - **azure-ai-inference**: usada para conversar com um modelo de IA generativa.

    ```
    pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

1. Insira o seguinte comando para editar o arquivo de configuração Python **.env** que foi fornecido:

    ```
    code .env
    ```

    O arquivo abrirá em um editor de código.

1. No arquivo de código, substitua o espaço reservado **your_project_endpoint** pela cadeia de conexão do projeto (copiada da página **Visão geral** do projeto no portal da Fábrica de IA do Azure) e o espaço reservado **your_model_deployment** pelo nome atribuído à implantação do modelo Phi-4.
1. Depois de substituir os espaços reservados, use o comando **CTRL+S** para salvar suas alterações e, em seguida, use o comando **CTRL+Q** para fechar o editor de código enquanto mantém a linha de comando do Cloud Shell aberta.

### Escrever código para se conectar ao projeto e conversar com o modelo

> **Dica**: ao adicionar código ao arquivo de código Python, certifique-se de manter o recuo correto.

1. Insira o seguinte comando para editar o arquivo de código Python **chat-app.py** que foi fornecido:

    ```
    code chat-app.py
    ```

1. No arquivo de código, observe as instruções de **importação** existentes que foram adicionadas na parte superior do arquivo. Em seguida, no comentário **# Adicionar referência de Projetos de IA**, adicione o seguinte código para fazer referência à biblioteca de Projetos de IA do Azure:

    ```python
    from azure.ai.projects import AIProjectClient
    ```

1. Na função **main**, no comentário **# Obter definições de configuração**, observe que o código carrega os valores da cadeia de conexão do projeto e do nome de implantação do modelo que você definiu no arquivo **.env**.
1. No comentário **# Inicializar o cliente do projeto**, adicione o seguinte código para se conectar ao seu projeto da Fábrica de IA do Azure usando as credenciais do Azure com as quais você entrou:

    ```python
    project = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential()
        )
    ```
    
1. No comentário **# Obter um cliente de chat**, adicione o seguinte código para criar um objeto de cliente para conversar com um modelo:

    ```python
    chat = project.inference.get_chat_completions_client()
    ```

1. Observe que o código inclui um loop para permitir que um usuário insira um prompt até digitar "sair". Em seguida, na seção de loop, no comentário **# Obter uma conclusão de chat**, adicione o seguinte código para enviar o prompt e recuperar a conclusão do seu modelo:

    ```python
    response = chat.complete(
        model=model_deployment,
        messages=[
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."},
            {"role": "user", "content": input_text},
            ],
        )
    print(response.choices[0].message.content)
    ```

1. Use o comando **CTRL+S** para salvar suas alterações no arquivo de código e, em seguida, use o comando **CTRL+Q** para fechar o editor de código enquanto mantém a linha de comando do Cloud Shell aberta.

### Executar o aplicativo de chat

1. No painel da linha de comando do Cloud Shell, insira o seguinte comando para executar o código Python:

    ```
    python chat-app.py
    ```

1. Quando solicitado, insira uma pergunta, como `What is the fastest animal on Earth?`, e revise a resposta do seu modelo de IA generativa.
1. Tente mais algumas perguntas. Quando terminar, pressione `quit` para encerrar o programa.

## Resumo

Neste exercício, você usou o SDK da Fábrica de IA do Azure para criar um aplicativo cliente para um modelo de IA generativa implantado em um projeto da Fábrica de IA do Azure.

## Limpar

Se tiver terminado de explorar o portal do Azure AI Foundry, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Retorne à guia do navegador que contém o portal do Azure (ou abra novamente o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` em uma nova guia do navegador) e exiba o conteúdo do grupo de recursos em que você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.
