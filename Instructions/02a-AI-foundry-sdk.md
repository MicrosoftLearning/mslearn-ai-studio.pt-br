---
lab:
  title: Criar um aplicativo de chat de IA generativa
  description: Saiba como usar o SDK da Fábrica de IA do Azure para criar um aplicativo que se conecta ao seu projeto e chat com um modelo de linguagem.
---

# Criar um aplicativo de chat de IA generativa

Neste exercício, você usa o SDK da Fábrica de IA do Azure para criar um aplicativo de chat simples que se conecta a um projeto e chat com um modelo de linguagem.

Este exercício levará aproximadamente **30** minutos.

## Criar um projeto do Azure AI Foundry

Vamos começar criando um projeto da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir:

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. Na home page, selecione **+Criar projeto**.
1. No assistente **Criar um projeto**, insira um nome de projeto adequado (por exemplo, `my-ai-project`) e revise os recursos do Azure que serão criados automaticamente para dar suporte ao seu projeto.
1. Selecione **Personalizar** e especifique as seguintes configurações para o hub:
    - **Nome do hub**: *um nome exclusivo – por exemplo `my-ai-hub`*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *crie um novo grupo de recursos com um nome exclusivo (por exemplo, `my-ai-resources`) ou selecione um existente*
    - **Região**: selecione uma das seguintes regiões\*:
        - Leste dos EUA
        - Leste dos EUA 2
        - Centro-Norte dos EUA
        - Centro-Sul dos Estados Unidos
        - Suécia Central
        - Oeste dos EUA
        - Oeste dos EUA 3
    - **Conectar os Serviços de IA do Azure ou o OpenAI do Azure:*** crie um novo recurso de Serviços de IA com um nome apropriado (por exemplo, `my-ai-services`) ou use um existente*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* No momento em que este artigo foi escrito, o modelo *Phi-4* da Microsoft que usaremos neste exercício está disponível nessas regiões. Você pode verificar a disponibilidade regional mais recente para modelos específicos na [documentação da Fábrica de IA do Azure](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability). No caso de um limite de cota regional ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente.

1. Clique em **Avançar** e revise a configuração. Em seguida, selecione **Criar** e aguarde a conclusão do processo.
1. Quando o projeto for criado, feche todas as dicas exibidas e examine a página do projeto no Portal da Fábrica de IA do Azure, que deve ser semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um projeto IA do Azure no Portal da Fábrica de IA do Azure.](./media/ai-foundry-project.png)

## Implantar um modelo de IA generativa

Agora está tudo pronto para implantar um modelo de linguagem de IA generativa para dar suporte ao seu aplicativo de chat. Neste exemplo, você usará o modelo Microsoft Phi-4; mas os princípios são os mesmos para qualquer modelo.

1. Na barra de ferramentas no canto superior direito da página do projeto da Fábrica de IA do Azure, use o ícone **Recursos de visualização** para ativar o recurso **Implantar modelos para o serviço de inferência de modelo de IA do Azure**. Esse recurso garante que a implantação do modelo esteja disponível para o serviço de inferência de IA do Azure, que você usará no código do aplicativo.
1. No painel à esquerda do seu projeto, na seção **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Na página **Modelos + pontos extremidades**, na guia **Implantações de modelo**, no menu **+ Implantar modelo**, selecione **Implantar modelo base**.
1. Procure o modelo **Phi-4** na lista e, em seguida, selecione-o e confirme-o.
1. Concorde com o contrato de licença, se solicitado, e implante o modelo com as seguintes configurações selecionando **Personalizar** nos detalhes da implantação:
    - **Nome de implantação**: *um nome exclusivo para sua implantação de modelo – por exemplo `phi-4-model` (lembre-se do nome que você atribui, você precisará dele mais tarde*)
    - **Tipo de implantação**: padrão global
    - **Detalhes da implantação**: *use as configurações padrão*
1. Aguarde que o estado de provisionamento de implantação seja **Concluído**.

## Criar um aplicativo cliente para conversar com o modelo

Agora que você implantou um modelo, pode usar o SDK da Fábrica de IA do Azure para desenvolver um aplicativo que se comunica com ele.

> **Dica**: você pode optar por desenvolver sua solução usando Python ou Microsoft C#. Siga as instruções na seção apropriada para o idioma escolhido.

### Preparar a configuração de aplicativo

1. No Portal da Fábrica de IA do Azure, visualize a página **Visão geral** do seu projeto.
1. Na área **Detalhes do projeto**, observe a **Cadeia de conexão do projeto**. Você usará essa cadeia de conexão para se conectar ao seu projeto em um aplicativo cliente.
1. Abra uma nova guia do navegador (mantendo o portal da Fábrica de IA do Azure aberto na guia existente). Em seguida, na nova guia, navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`; efetue login com suas credenciais do Azure, se solicitado.
1. Use o botão **[\>_]** à direita da barra de pesquisa na parte superior da página para criar um Cloud Shell no portal do Azure selecionando um ambiente do ***PowerShell***. O Cloud Shell fornece uma interface de linha de comando em um painel na parte inferior do portal do Azure.

    > **Observação**: se você já criou um Cloud Shell que usa um ambiente *Bash*, alterne-o para o ***PowerShell***.

1. Na barra de ferramentas do Cloud Shell, no menu **Configurações**, selecione **Ir para a versão clássica** (isso é necessário para usar o editor de código).

    > **Dica**: conforme você colar comandos no cloudshell, a saída pode ocupar uma grande quantidade do espaço da tela. Você pode limpar a tela digitando o comando `cls` para facilitar o foco em cada tarefa.

1. No painel do PowerShell, insira os seguintes comandos para clonar o repositório GitHub para este exercício:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

> **Observação**: siga as etapas para a linguagem de programação escolhida.

1. Após o repositório ser clonado, navegue até a pasta que contém os arquivos de código do aplicativo de chat:  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/c-sharp
    ```

1. No painel de linha de comando do Cloud Shell, digite o seguinte comando para instalar as bibliotecas que você usará:

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
    ```
    

1. Digite o seguinte comando para editar o arquivo de configuração que foi fornecido:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    O arquivo é aberto em um editor de código.

1. No arquivo de código, substitua o espaço reservado **your_project_endpoint** pela cadeia de conexão do seu projeto (copiada da página **Visão Geral** do projeto no portal da Fábrica de IA do Azure) e o espaço reservado **your_model_deployment** pelo nome que você atribuiu à implantação do seu modelo Phi-4.
1. Depois de substituir os espaços reservados, use o comando **CTRL+S** para salvar suas alterações e, em seguida, use o comando **CTRL+Q** para fechar o editor de código, mantendo a linha de comando do Cloud Shell aberta.

### Escrever código para se conectar ao seu projeto e conversar com seu modelo

> **Dica**: ao adicionar código, certifique-se de manter o recuo correto.

1. Digite o seguinte comando para editar o arquivo de código que foi fornecido:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. No arquivo de código, observe as instruções existentes que foram adicionadas na parte superior do arquivo para importar os namespaces do SDK necessários. Em seguida, no comentário **Adicionar referências**, adicione o seguinte código para referenciar os namespaces nas bibliotecas que você instalou anteriormente:

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import SystemMessage, UserMessage
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. Na função **main**, no comentário **Get configuration settings**, observe que o código carrega a cadeia de conexão do projeto e os valores do nome de implantação do modelo que você definiu no arquivo de configuração.
1. No comentário **Initialize the project client**, adicione o seguinte código para se conectar ao seu projeto da Fábrica de IA do Azure usando as credenciais do Azure com as quais você está conectado no momento:

    **Python**

    ```
   projectClient = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. No comentário **# Get a chat client**, adicione o seguinte código para criar um objeto cliente para conversar com um modelo:

    **Python**

    ```
   chat = projectClient.inference.get_chat_completions_client()
    ```

    **C#**

    ```
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

1. Observe que o código inclui um loop para permitir que o usuário insira um prompt até digitar "quit". Em seguida, na seção de loop, sob o comentário **# Get a chat completion**, adicione o seguinte código para enviar o prompt e recuperar a conclusão do seu modelo:

    **Python**

    ```python
   response = chat.complete(
       model=model_deployment,
       messages=[
           SystemMessage("You are a helpful AI assistant that answers questions."),
           UserMessage(input_text)
       ])
   print(response.choices[0].message.content
```

    **C#**

    ```
   var requestOptions = new ChatCompletionsOptions()
   {
       Model = model_deployment,
       Messages =
           {
               new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions."),
               new ChatRequestUserMessage(input_text),
           }
   };
    
   Response<ChatCompletions> response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

1. Use o comando **CTRL+S** para salvar suas alterações no arquivo de código e, em seguida, use o comando **CTRL+Q** para fechar o editor de código, mantendo a linha de comando do Cloud Shell aberta.

### Executar o aplicativo de chat

1. No painel de linha de comando do Cloud Shell, insira o seguinte comando para executar o aplicativo:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Quando solicitado, insira uma pergunta, como `What is the fastest animal on Earth?` e analise a resposta do seu modelo de IA generativa.
1. Experimente usar mais algumas perguntas. Quando terminar, digite `quit` para sair do programa.

## Resumo

Neste exercício, você usou o SDK da Fábrica de IA do Azure para criar um aplicativo cliente para um modelo de IA generativa que você implantou em um projeto da Fábrica de IA do Azure.

## Limpar

Se tiver terminado de explorar o portal do Azure AI Foundry, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Retorne à guia do navegador que contém o portal do Azure (ou reabra o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` em uma nova guia do navegador) e visualize o conteúdo do grupo de recursos onde você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.
