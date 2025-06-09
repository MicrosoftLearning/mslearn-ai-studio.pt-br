---
lab:
  title: Crie um aplicativo de IA generativa que use seus próprios dados
  description: Saiba como usar o modelo RAG (Geração Aumentada de Recuperação) para criar um aplicativo de chat que fundamenta prompts usando seus próprios dados.
---

# Crie um aplicativo de IA generativa que use seus próprios dados

A Geração Aumentada de Recuperação (RAG) é uma técnica usada para compilar aplicativos que integram dados de fontes de dados personalizadas em um prompt para um modelo de IA generativa. O RAG é um padrão comumente usado para desenvolver aplicativos de IA generativa – aplicativos baseados em chat que usam um modelo de linguagem para interpretar entradas e gerar respostas apropriadas.

Neste exercício, você usará a Fábrica de IA do Azure para integrar dados personalizados em uma solução de IA generativa.

Este exercício levará, aproximadamente, **45** minutos.

> **Observação**: este exercício é baseado em serviços de pré-lançamento, que podem estar sujeitos a alterações.

## Crie um hub e projeto da Fábrica de IA do Azure

Os recursos da Fábrica de IA do Azure que usaremos neste exercício requerem um projeto baseado em um recurso de *hub* da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir (feche o painel **Ajuda** se estiver aberto):

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. No navegador, navegue até `https://ai.azure.com/managementCenter/allResources` e clique em **Criar**. Em seguida, escolha a opção para criar um novo **Recurso do hub de IA**.
1. No assistente **Criar um projeto**, insira um nome válido para o projeto e, se um hub existente for sugerido, clique na opção para criar um novo e expanda **Opções avançadas** para especificar as seguintes configurações para o projeto:
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Nome do hub**: um nome válido para o hub
    - **Localização**: Leste dos EUA 2 ou Suécia Central\*

    > \* Alguns recursos da IA do Azure são restritos por cotas de modelo regional. Caso um limite de cota seja excedido posteriormente no exercício, é possível que você precise criar outro recurso em uma região diferente.

1. Aguarde até que seu projeto seja criado.

## Implantar modelos

Você precisará de dois modelos para implementar sua solução:

- Um modelo de *inserção* para vetorizar dados de texto para indexação e processamento eficientes.
- Um modelo que pode gerar respostas em linguagem natural para perguntas com base em seus dados.

1. No portal do Azure AI Foundry, em seu projeto, no painel de navegação à esquerda, em **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Crie uma nova implantação do modelo **text-embedding-ada-002** com as seguintes configurações selecionando **Personalizar** no assistente do modelo de implantação:

    - **Nome da implantação**: *Um nome válido para sua implantação de modelo*
    - **Tipo de implantação**: padrão global
    - **Versão do modelo**: *selecione a versão padrão*
    - **Recurso de IA conectado**: *escolha o recurso criado anteriormente*
    - **Limite de taxa de tokens por minuto (milhares):** 50 mil *(ou o máximo disponível em sua assinatura, se inferior a 50 mil)*
    - **Filtro de conteúdo**: DefaultV2

    > **Observação**: se o local atual do recurso de IA não tiver cota disponível para o modelo que você deseja implantar, será solicitado a escolher um local diferente onde um novo recurso de IA será criado e conectado ao seu projeto.

1. Retorne à página **Modelos + pontos de extremidade** e repita as etapas anteriores para implantar um modelo **gpt-4o** usando uma implantação **Padrão Global** da versão mais recente com um limite de taxa de TPM de **50 mil** (ou o máximo disponível em sua assinatura, se for menor que 50 mil).

    > **Observação**: A redução dos Tokens por Minuto (TPM) ajuda a evitar o uso excessivo da cota disponível na assinatura que você estiver usando. 50.000 TPM são suficientes para os dados usados neste exercício.

## Adicionar dados ao seu projeto

Os dados para o seu aplicativo consistem em um conjunto de folhetos de viagem em formato PDF da agência de viagens fictícia *Margie's Travel*. Vamos adicioná-los ao projeto.

1. Em uma nova guia do navegador, baixe o [arquivo compactado de folhetos](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) em `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` e extraia-o para uma pasta chamada **folhetos** em seu sistema de arquivos local.
1. No portal do Azure IA Foundry, em seu projeto, no painel de navegação à esquerda, em **Maus ativos**, selecione a página **Dados + índices**.
1. Selecione **+ Novos dados**.
1. No assistente **Adicionar dados**, expanda o menu suspenso para selecionar **Carregar arquivos/pastas**.
1. Selecione **Carregar pasta** e carregue a pasta **brochures**. Aguarde até que todos os arquivos da pasta sejam listados.
1. Selecione **Avançar** e defina o nome dos dados como `brochures`.
1. Aguarde o upload da pasta e observe que ela contém vários arquivos .pdf.

## Crie um índice para seus dados

Agora que você adicionou uma fonte de dados ao seu projeto, pode usá-la para criar um índice em seu recurso de Pesquisa de IA do Azure.

1. No portal do Azure IA Foundry, em seu projeto, no painel de navegação à esquerda, em **Maus ativos**, selecione a página **Dados + índices**.
1. Na guia **Índices**, adicione um novo índice com as seguintes configurações:
    - **Local de origem**:
        - **Fonte de dados**: dados na Fábrica de IA do Azure
            - *Selecione a fonte de dados dos **folhetos***
    - **Configuração de índice**:
        - **Selecione o serviço Pesquisa de IA do Azure**: *crie um novo recurso da Pesquisa de IA do Azure com as seguintes configurações*:
            - **Assinatura**: *a sua assinatura do Azure*
            - **Grupo de recursos**: *o mesmo grupo de recursos do Hub de IA*
            - **Nome do serviço**: *um nome válido para o recurso da Pesquisa de IA*
            - **Local**: *o mesmo local do Hub de IA*
            - **Tipo de preço**: Básico
            
            Aguarde a criação do recurso da Pesquisa de IA Em seguida, retorne à Fábrica de IA do Azure e conclua a configuração do índice clicando em **Conectar outro recurso da Pesquisa de IA do Azure** e adicionando uma conexão ao recurso da Pesquisa de IA do Azure que você acabou de criar.
 
        - **Índice de vetor**: `brochures-index`
        - **Máquina virtual**: Selecionar automaticamente
    - **Configurações de pesquisa**:
        - **Configurações de vetor**: Adicione busca em vetores a este recurso de pesquisa
        - **Conexão OpenAI do Azure**: *selecione o recurso OpenAI do Azure padrão no seu hub.*
        - **Modelo de incorporação**: text-embedding-ada-002
        - **Implantação do modelo de incorporação**: *A sua implantação* do *modelo*text-embedding-ada-002

1. Crie o índice de vetor e aguarde a conclusão do processo de indexação, o que pode demorar um pouco, dependendo dos recursos de computação disponíveis em sua assinatura.

    A operação de criação de índice consiste nos seguintes trabalhos:

    - Extraia, particione e insira os tokens de texto nos dados de folhetos.
    - Crie o índice do IA do Azure Search.
    - Registrar o ativo de índice.

    > **Dica**: enquanto você espera a criação do índice, por que não dar uma olhada nos folhetos que você baixou para se familiarizar com o conteúdo?

## Testar o índice no playground

Antes de usar seu índice em um prompt flow baseado em RAG, vamos verificar se ele pode ser usado para afetar respostas de IA generativa.

1. No painel de navegação à esquerda, selecione a página **Playgrounds** e abra o playground **Chat**.
1. Na página do playground Chat, no painel Configuração, verifique se a implantação do modelo **gpt-4o** está selecionada. Em seguida, no painel de sessão de chat principal, envie o prompt `Where can I stay in New York?`
1. Revise a resposta, que deve ser uma resposta genérica do modelo sem dados do índice.
1. No painel de configurações, expanda o campo **Adicionar seus dados** e, em seguida, adicione o índice de projeto **brochures-index** e selecione o tipo de pesquisa **híbrido (vetor + palavra-chave)**.

   > **Dica**: em alguns casos, os índices recém-criados podem não estar disponíveis imediatamente. Atualizar o navegador geralmente ajuda, mas se você ainda estiver enfrentando o problema de não conseguir encontrar o índice, talvez seja necessário aguardar até que o índice seja reconhecido.

1. Depois que o índice tiver sido adicionado e a sessão de chat for reiniciada, reenvie o prompt `Where can I stay in New York?`
1. Revise a resposta, que deve ser baseada nos dados do índice.

<!-- DEPRECATED STEPS

## Create a RAG client app with the Azure AI Foundry and Azure OpenAI SDKs

Now that you have a working index, you can use the Azure AI Foundry and Azure OpenAI SDKs to implement the RAG pattern in a client application. Let's explore the code to accomplish this in a simple example.

> **Tip**: You can choose to develop your RAG solution using Python or Microsoft C#. Follow the instructions in the appropriate section for your chosen language.

### Prepare the application configuration

1. In the Azure AI Foundry portal, view the **Overview** page for your project.
1. In the **Project details** area, note the **Project connection string**. You'll use this connection string to connect to your project in a client application.
1. Return to the browser tab containing the Azure portal (keeping the Azure AI Foundry portal open in the existing tab).
1. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a ***PowerShell*** environment with no storage in your subscription.

    The cloud shell provides a command-line interface in a pane at the bottom of the Azure portal. You can resize or maximize this pane to make it easier to work in.

    > **Note**: If you have previously created a cloud shell that uses a *Bash* environment, switch it to ***PowerShell***.

1. In the cloud shell toolbar, in the **Settings** menu, select **Go to Classic version** (this is required to use the code editor).

    **<font color="red">Ensure you've switched to the classic version of the cloud shell before continuing.</font>**

1. In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files for this exercise (type the command, or copy it to the clipboard and then right-click in the command line and paste as plain text):

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Tip**: As you paste commands into the cloudshell, the output may take up a large amount of the screen buffer. You can clear the screen by entering the `cls` command to make it easier to focus on each task.

1. After the repo has been cloned, navigate to the folder containing the chat application code files:

    > **Note**: Follow the steps for your chosen programming language.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. In the cloud shell command-line pane, enter the following command to install the libraries you'll use:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
    ```
    

1. Enter the following command to edit the configuration file that has been provided:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    The file is opened in a code editor.

1. In the code file, replace the following placeholders: 
    - **your_project_connection_string**: Replace with the connection string for your project (copied from the project **Overview** page in the Azure AI Foundry portal).
    - **your_gpt_model_deployment** Replace with the name you assigned to your **gpt-4o** model deployment.
    - **your_embedding_model_deployment**: Replace with the name you assigned to your **text-embedding-ada-002** model deployment.
    - **your_index**: Replace with your index name (which should be `brochures-index`).
1. After you've replaced the placeholders, in the code editor, use the **CTRL+S** command or **Right-click > Save** to save your changes and then use the **CTRL+Q** command or **Right-click > Quit** to close the code editor while keeping the cloud shell command line open.

### Explore code to implement the RAG pattern

1. Enter the following command to edit the code file that has been provided:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Review the code in the file, noting that it:
    - Uses the Azure AI Foundry SDK to connect to your project (using the project connection string)
    - Creates an authenticated Azure OpenAI client from your project connection.
    - Retrieves the default Azure AI Search connection from your project so it can determine the endpoint and key for your Azure AI Search service.
    - Creates a suitable system message.
    - Submits a prompt (including the system and a user message based on the user input) to the Azure OpenAI client, adding:
        - Connection details for the Azure AI Search index to be queried.
        - Details of the embedding model to be used to vectorize the query\*.
    - Displays the response from the grounded prompt.
    - Adds the response to the chat history.

    \* *The query for the search index is based on the prompt, and is used to find relevant text in the indexed documents. You can use a keyword-based search that submits the query as text, but using a vector-based search can be more efficient - hence the use of an embedding model to vectorize the query text before submitting it.*

1. Use the **CTRL+Q** command to close the code editor without saving any changes, while keeping the cloud shell command line open.

### Run the chat application

1. In the cloud shell command-line pane, enter the following command to run the app:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. When prompted, enter a question, such as `Where should I go on vacation to see architecture?` and review the response from your generative AI model.

    Note that the response includes source references to indicate the indexed data in which the answer was found.

1. Try a follow-up question, for example `Where can I stay there?`

1. When you're finished, enter `quit` to exit the program. Then close the cloud shell pane.

-->

## Limpar

Para evitar custos desnecessários do Azure e utilização de recursos, você deve remover os recursos implantados neste exercício.

1. Se você terminou de explorar o Azure IA Foundry, retorne ao [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` e entre usando suas credenciais do Azure, se necessário. Em seguida, elimine os recursos no grupo de recursos onde aprovisionou os seus recursos IA do Azure Search e IA do Azure.
