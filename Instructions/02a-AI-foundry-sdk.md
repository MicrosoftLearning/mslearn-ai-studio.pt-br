---
lab:
  title: Criar um aplicativo de chat de IA generativa
  description: Saiba como usar o SDK da Fábrica de IA do Azure para criar um aplicativo que se conecta ao seu projeto e chat com um modelo de linguagem.
---

# Criar um aplicativo de chat de IA generativa

Neste exercício, você usa o SDK da Fábrica de IA do Azure para criar um aplicativo de chat simples que se conecta a um projeto e chat com um modelo de linguagem.

Este exercício leva aproximadamente **40** minutos.

> **Observação**: este exercício é baseado em SDKs de pré-lançamento, que podem estar sujeitos a alterações. Quando necessário, usamos versões específicas de pacotes que podem não refletir as versões mais recentes disponíveis.

## Criar um projeto do Azure AI Foundry

Vamos começar criando um projeto da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir (feche o painel **Ajuda** se estiver aberto):

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. Na home page, selecione **+Criar projeto**.
1. No assistente **Criar um projeto**, insira um nome de projeto adequado (por exemplo, ) e, se um hub existente for sugerido, escolha a opção de criar um novo. Em seguida, examine os recursos do Azure que serão criados automaticamente para dar suporte ao hub e ao projeto.
1. Selecione **Personalizar** e especifique as seguintes configurações para o hub:
    - **Nome do hub**: *um nome válido para o seu hub*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Localização**: selecione **Ajude-me a escolher** e então selecione **gpt-4o** na janela do auxiliar de localização e use a região recomendada\*
    - **Conectar os Serviços de IA do Azure ou o OpenAI do Azure**: *Criar um novo recurso de Serviços de IA*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restritos por cotas regionais. Caso um limite de cota seja excedido posteriormente no exercício, é possível que você precise criar outro recurso em uma região diferente.

1. Clique em **Avançar** e revise a configuração. Em seguida, selecione **Criar** e aguarde a conclusão do processo.
1. Quando o projeto for criado, feche todas as dicas exibidas e examine a página do projeto no Portal da Fábrica de IA do Azure, que deve ser semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um projeto IA do Azure no Portal da Fábrica de IA do Azure.](./media/ai-foundry-project.png)

## Implantar um modelo de IA generativa

Agora está tudo pronto para implantar um modelo de linguagem de IA generativa para dar suporte ao seu aplicativo de chat. Neste exemplo, você usará o modelo OpenAI gpt-4; mas os princípios são os mesmos para qualquer modelo.

1. Na barra de ferramentas no canto superior direito da página do projeto do Azure AI Foundry, use o ícone **Preview features** (**&#9215;**) para garantir que o recurso **Implantar modelos no serviço de inferência de modelos de IA do Azure** seja habilitado. Esse recurso garante que a implantação do modelo esteja disponível para o serviço de inferência de IA do Azure, que você usará no código do aplicativo.
1. No painel à esquerda do seu projeto, na seção **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Na página **Modelos + pontos extremidades**, na guia **Implantações de modelo**, no menu **+ Implantar modelo**, selecione **Implantar modelo base**.
1. Procure o modelo **gpt-4** na lista, selecione-o e confirme-o.
1. Crie uma nova implantação do modelo com as seguintes configurações selecionando **Personalizar** nos detalhes de implantação:
    - **Nome da implantação**: *Um nome válido para sua implantação de modelo*
    - **Tipo de implantação**: padrão global
    - **Atualização automática de versão**: Ativado
    - **Versão do modelo**: *selecione a versão mais recente disponivel*
    - **Recurso de IA conectado**: *selecione a sua conexão de recursos do OpenAI do Azure*
    - **Limite de taxa de tokens por minuto (milhares):** 50 mil *(ou o máximo disponível em sua assinatura, se inferior a 50 mil)*
    - **Filtro de conteúdo**: DefaultV2

    > **Observação**: A redução do TPM ajuda a evitar o uso excessivo da cota disponível na assinatura que você está usando. 50.000 TPM são suficientes para os dados usados neste exercício. Se a sua cota disponível for menor do que isso, você poderá concluir o exercício, mas poderá ocorrer erros se o limite de taxa for excedido.

1. Aguarde até que a implantação seja concluída.

## Criar um aplicativo cliente para conversar com o modelo

Agora que você implantou um modelo, pode usar o SDK da Fábrica de IA do Azure para desenvolver um aplicativo que se comunique com ele.

> **Dica**: você pode optar por desenvolver sua solução usando Python ou Microsoft C#. Siga as instruções na seção apropriada para o idioma escolhido.

### Preparar a configuração de aplicativo

1. No Portal da Fábrica de IA do Azure, visualize a página **Visão geral** do seu projeto.
1. Na área **Detalhes do projeto**, observe a **Cadeia de conexão do projeto**. Você usará essa cadeia de conexão para se conectar ao seu projeto em um aplicativo cliente.
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

1. Após o repositório ser clonado, navegue até a pasta que contém os arquivos de código do aplicativo de chat:

    Use o comando abaixo, dependendo da linguagem de programação de sua escolha.

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
   python -m venv labenv
   ./labenv/bin/Activate.ps1
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

1. No arquivo de código, substitua o espaço reservado **your_project_connection_string** pela cadeia de conexão do seu projeto (copiada da página **Visão Geral** do projeto no portal da Fábrica de IA do Azure), e o espaço reservado **your_model_deployment** pelo nome que você atribuiu à implantação do seu modelo gpt-4.
1. Depois de substituir os espaços reservados, use o comando **CTRL+S** ou **botão direito do mouse > Salvar** para salvar as suas alterações e, em seguida, use o comando **CTRL+Q** ou **botão direito do mouse > Sair** para fechar o editor de código, mantendo a linha de comando do Cloud Shell aberta.

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

    ```python
   # Add references
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import SystemMessage, UserMessage, AssistantMessage
    ```

    **C#**

    ```csharp
   // Add references
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. Na função **main**, no comentário **Get configuration settings**, observe que o código carrega a cadeia de conexão do projeto e os valores do nome de implantação do modelo que você definiu no arquivo de configuração.
1. No comentário **Initialize the project client**, adicione o seguinte código para se conectar ao seu projeto da Fábrica de IA do Azure usando as credenciais do Azure com as quais você está conectado no momento:

    > **Dica**: Tenha cuidado para manter o nível de recuo correto no seu código.

    **Python**

    ```python
   # Initialize the project client
   projectClient = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
   // Initialize the project client
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. No comentário **Get a chat client**, adicione o seguinte código para criar um objeto cliente para conversar com um modelo:

    **Python**

    ```python
   # Get a chat client
   chat = projectClient.inference.get_chat_completions_client()
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

    > **Observação**: esse código usa o cliente de projeto da Fábrica de IA do Azure para criar uma conexão segura com o ponto de extremidade de serviço padrão da Inferência de Modelo da IA do Azure associada ao seu projeto. Você também pode se conectar *diretamente* ao ponto de extremidade usando o SDK de Inferência de Modelo de IA do Azure, especificando o URI do ponto de extremidade exibido para a conexão de serviço no portal da Fábrica de IA do Azure ou na página de recursos dos Serviços de IA do Azure correspondente no portal do Azure e usando uma chave de autenticação ou token de credencial do Entra. Para obter mais informações sobre como se conectar ao serviço de Inferência de Modelos de IA do Azure, consulte [API da Inferência de Modelos de IA do Azure](https://learn.microsoft.com/azure/machine-learning/reference-model-inference-api).

1. Localize o comentário **Initialize prompt with system message** e adicione o código a seguir para inicializar uma coleção de mensagens com um prompt do sistema.

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
            SystemMessage("You are a helpful AI assistant that answers questions.")
        ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
   var prompt = new List<ChatRequestMessage>(){
                    new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions.")
                };
    ```

1. Observe que o código inclui um loop para permitir que o usuário insira um prompt até digitar "quit". Em seguida, na seção de loop, localize o comentário **Get a chat completion** e adicione o seguinte código para adicionar a entrada de usuário ao prompt, obter a conclusão do seu modelo e adicionar a conclusão ao prompt (para que você mantenha o histórico de chat em iterações futuras):

    **Python**

    ```python
   # Get a chat completion
   prompt.append(UserMessage(input_text))
   response = chat.complete(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append(AssistantMessage(completion))
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new ChatRequestUserMessage(input_text));
   var requestOptions = new ChatCompletionsOptions()
   {
       Model = model_deployment,
       Messages = prompt
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   var completion = response.Value.Content;
   Console.WriteLine(completion);
   prompt.Add(new ChatRequestAssistantMessage(completion));
    ```

1. Use o comando **CTRL+S** para salvar suas alterações no arquivo de código.

### Executar o aplicativo de chat

1. No painel da linha de comando do Cloud Shell, abaixo do editor de código, insira o seguinte comando para executar o aplicativo:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Quando solicitado, insira uma pergunta, como `What is the fastest animal on Earth?` e analise a resposta do seu modelo de IA generativa.
1. Tente algumas perguntas de acompanhamento, como `Where can I see one?` ou `Are they endangered?`. A conversa deve continuar, usando o histórico de bate-papo como contexto para cada iteração.
1. Quando terminar, digite `quit` para sair do programa.

> **Dica**: se o aplicativo falhar porque o limite de taxa foi excedido. Aguarde alguns segundos e tente novamente. Se não houver cota suficiente disponível em sua assinatura, o modelo poderá não ser capaz de responder.

## Use o SDK do OpenAI do Azure

Seu aplicativo cliente é criado usando a SDK da Inferência de Modelos de IA do Azure, o que significa que ele pode ser usado com qualquer modelo implantado no serviço Inferência de Modelos de IA do Azure. O modelo implantado é um modelo OpenAI GPT, que você também pode consumir usando o OpenAI SDK.

Vamos fazer algumas modificações de código para ver como implementar um aplicativo de bate-papo usando o OpenAI SDK.

1. Na linha de comando do Cloud Shell para sua pasta de código (*Python* ou *C#*), use o seguinte comando para instalar o pacote necessário:

    **Python**

    ```
   pip install openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.6
   dotnet add package Azure.AI.OpenAI --prerelease
    ```

> **Observação**: uma versão de pré-lançamento diferente do pacote Azure.AI.Projects é necessária como solução provisória de algumas incompatibilidades com o SDK da Inferência de Modelo de IA do Azure.

1. Se o seu arqivo de código (*chat-app.py* ou *Program.cs*) ainda não estiver aberto, digite o seguinte comando para abri-lo no editor de código:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Adicione a(s) seguinte(s) referência(s) na parte superior do arquivo de código:

    **Python**

    ```python
   import openai
    ```

    **C#**

    ```csharp
   using OpenAI.Chat;
   using Azure.AI.OpenAI;
    ```

1. Localize o comentário **Get a chat client** e modifique o código usado para criar um objeto de cliente da seguinte maneira:

    **Python**

    ```python
   # Get a chat client 
   openai_client = projectClient.inference.get_azure_openai_client(api_version="2024-10-21")
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatClient openaiClient = projectClient.GetAzureOpenAIChatClient(model_deployment);
    ```

    > **Observação**: esse código usa o cliente de projeto da Fábrica de IA do Azure para criar uma conexão segura com o ponto de extremidade de serviço padrão do Azure OpenAI associado ao seu projeto. Você também pode se conectar *diretamente* ao ponto de extremidade usando o SDK do OpenAI do Azure, especificando o URI do ponto de extremidade exibido para a conexão de serviço no portal da Fábrica de IA do Azure ou na página de recursos do Azure OpenAI ou dos Serviços de IA correspondente no portal do Azure e usando uma chave de autenticação ou token de credencial do Entra. Para obter mais informações sobre como se conectar ao serviço OpenAI do Azure, consulte [Linguagens de programação com suporte do Azure OpenAI](https://learn.microsoft.com/azure/ai-services/openai/supported-languages).

1. Localize o comentário **Initialize prompt with system message** e modifique o código para inicializar uma coleção de mensagens com um prompt do sistema da seguinte maneira:

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
        {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
    ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
    var prompt = new List<ChatMessage>(){
        new SystemChatMessage("You are a helpful AI assistant that answers questions.")
    };
    ```

1. Localize o comentário **Get a chat completion** e altere o código, adicionando a entrada de usuário ao prompt, obtenha a conclusão do seu modelo e adicione-a ao prompt da seguinte maneira:

    **Python**

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

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new UserChatMessage(input_text));
   ChatCompletion completion = openaiClient.CompleteChat(prompt);
   var completionText = completion.Content[0].Text;
   Console.WriteLine(completionText);
   prompt.Add(new AssistantChatMessage(completionText));
    ```

1. Use o comando **CTRL+S** para salvar suas alterações no arquivo de código.

1. No painel da linha de comando do Cloud Shell, abaixo do editor de código, insira o seguinte comando para executar o aplicativo:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Teste o aplicativo enviando perguntas como antes. Quando terminar, digite `quit` para sair do programa.

    > **Observação**: o SDK da Inferência de Modelo de IA do Azure e os SDKs do OpenAI usam classes e constructos de código semelhantes, portanto o código exigiu alterações mínimas. Você pode usar o SDK da Inferência de Modelos de IA do Azure com *qualquer* modelo implantado em um ponto de extremidade de serviço da Inferência de Modelos de IA do Azure. O SDK do OpenAI só funciona com modelos OpenAI, mas você pode usá-lo com modelos implantados em um ponto de extremidade de serviço da Inferência de Modelo de IA do Azure ou em um ponto de extremidade OpenAI do Azure.  

## Resumo

Neste exercício, você usou o SDK da Fábrica de IA do Azure para criar um aplicativo cliente para um modelo de IA generativa que você implantou em um projeto da Fábrica de IA do Azure.

## Limpar

Se tiver terminado de explorar o portal do Azure AI Foundry, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Retorne à guia do navegador que contém o portal do Azure (ou reabra o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` em uma nova guia do navegador) e visualize o conteúdo do grupo de recursos onde você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.
