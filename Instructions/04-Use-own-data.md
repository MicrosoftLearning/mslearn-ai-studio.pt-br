---
lab:
  title: Crie um aplicativo de IA generativa que use seus próprios dados
  description: Saiba como usar o modelo RAG (Geração Aumentada de Recuperação) para criar um aplicativo de chat que fundamenta prompts usando seus próprios dados.
---

# Crie um aplicativo de IA generativa que use seus próprios dados

A Geração Aumentada de Recuperação (RAG) é uma técnica usada para compilar aplicativos que integram dados de fontes de dados personalizadas em um prompt para um modelo de IA generativa. O RAG é um padrão comumente usado para desenvolver aplicativos de IA generativa – aplicativos baseados em chat que usam um modelo de linguagem para interpretar entradas e gerar respostas apropriadas.

Neste exercício, você usará o portal da Fábrica de IA do Azure e os SDKs da Fábrica de IA do Azure e o OpenAI do Azure para integrar dados personalizados em um aplicativo de IA generativa.

Este exercício levará, aproximadamente, **45** minutos.

> **Observação**: este exercício é baseado em SDKs de pré-lançamento, que podem estar sujeitos a alterações. Quando necessário, usamos versões específicas de pacotes que podem não refletir as versões mais recentes disponíveis.

## Criar um projeto do Azure AI Foundry

Vamos começar criando um projeto da Fábrica de IA do Azure e os recursos de serviço necessários para dar suporte ao uso de seus próprios dados, incluindo um recurso da Pesquisa de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir:

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. Na home page, selecione **+Criar projeto**.
1. No assistente **Criar um projeto**, insira um nome de projeto adequado (por exemplo, `my-ai-project`) e, se um hub existente for sugerido, escolha a opção de criar um novo. Em seguida, examine os recursos do Azure que serão criados automaticamente para dar suporte ao hub e ao projeto.
1. Selecione **Personalizar** e especifique as seguintes configurações para o hub:
    - **Nome do hub**: *um nome exclusivo – por exemplo `my-ai-hub`*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *crie um novo grupo de recursos com um nome exclusivo (por exemplo, `my-ai-resources`) ou selecione um existente*
    - **Localização**: selecione **Ajude-me a escolher** e, em seguida, selecione **gpt-4** e **text-embedding-ada-002** na janela do auxiliar de localização e use a região recomendada\*
    - **Conectar os Serviços de IA do Azure ou o OpenAI do Azure:*** crie um novo recurso de Serviços de IA com um nome apropriado (por exemplo, `my-ai-services`) ou use um existente*
    - **Conectar a Pesquisa de IA do Azure**: *crie um novo recurso da Pesquisa de IA do Azure com um nome exclusivo*

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. Caso um limite de cota seja atingido e nenhuma região seja recomendada para ambos os modelos, selecione apenas um deles e use sua região recomendada. Você criará outro recurso em uma região diferente para o segundo modelo posteriormente no exercício.

1. Clique em **Avançar** e revise a configuração. Em seguida, selecione **Criar** e aguarde a conclusão do processo.
1. Quando o projeto for criado, feche todas as dicas exibidas e examine a página do projeto **Visão geral** no Portal da Fábrica de IA do Azure, que deve ser semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um projeto IA do Azure no Portal da Fábrica de IA do Azure.](./media/ai-foundry-project.png)
   
## Implantar modelos

Você precisará de dois modelos para implementar sua solução:

- Um modelo de *inserção* para vetorizar dados de texto para indexação e processamento eficientes.
- Um modelo que pode gerar respostas em linguagem natural para perguntas com base em seus dados.

1. No portal do Azure AI Foundry, em seu projeto, no painel de navegação à esquerda, em **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Crie uma nova implantação do modelo **text-embedding-ada-002** com as seguintes configurações selecionando **Personalizar** no assistente do modelo de implantação:

    - **Nome da implantação**: `text-embedding-ada-002`
    - **Tipo de implantação**: Padrão
    - **Versão do modelo**: *selecione a versão padrão*
    - **Recurso de IA**: *escolha o recurso criado anteriormente*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: DefaultV2
    - **Habilitar cota dinâmica**: Desabilitado

    > **Observação**: se o local atual do recurso de IA não tiver cota disponível para o modelo que você deseja implantar, será solicitado a escolher um local diferente onde um novo recurso de IA será criado e conectado ao seu projeto.

1. Repita as etapas anteriores para implantar um modelo **gpt-4** com o nome `gpt-4` da implantação usando uma implantação **padrão** da versão padrão com um limite de taxa de TPM de 5K.

    > **Observação**: A redução dos Tokens por Minuto (TPM) ajuda a evitar o uso excessivo da cota disponível na assinatura que você estiver usando. 5.000 TPM são suficientes para os dados usados neste exercício.

## Adicionar dados ao seu projeto

Os dados para o seu copiloto consistem em um conjunto de folhetos de viagem em formato PDF da agência de viagens fictícia *Margie's Travel*. Vamos adicioná-los ao projeto.

1. Em uma nova guia do navegador, baixe o [arquivo compactado de folhetos](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) em `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` e extraia-o para uma pasta chamada **folhetos** em seu sistema de arquivos local.
1. No portal do Azure IA Foundry, em seu projeto, no painel de navegação à esquerda, em **Maus ativos**, selecione a página **Dados + índices**.
1. Selecione **+ Novos dados**.
1. No assistente **Adicionar dados**, expanda o menu suspenso para selecionar **Carregar arquivos/pastas**.
1. Selecione **Carregar pasta** e selecione a pasta **folhetos**.
1. Selecione **Avançar** e defina o nome dos dados como `brochures`.
1. Aguarde o upload da pasta e observe que ela contém vários arquivos .pdf.

## Crie um índice para seus dados

Agora que você adicionou uma fonte de dados ao seu projeto, pode usá-la para criar um índice em seu recurso de Pesquisa de IA do Azure.

1. No portal do Azure IA Foundry, em seu projeto, no painel de navegação à esquerda, em **Maus ativos**, selecione a página **Dados + índices**.
1. Na guia **Índices**, adicione um novo índice com as seguintes configurações:
    - **Local de origem**:
        - **Fonte de dados**: dados no portal do Azure AI Foundry
            - *Selecione a fonte de dados dos **folhetos***
    - **Configuração de índice**:
        - **Selecione o serviço Azure AI Search**: *Selecione a conexão do **AzureAISearch** com seu recurso de Pesquisa de IA do Azure*
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
1. Na página do playground Chat, no painel Configuração, verifique se a implantação do modelo **gpt-4** está selecionada. Em seguida, no painel de sessão de chat principal, envie o prompt `Where can I stay in New York?`
1. Revise a resposta, que deve ser uma resposta genérica do modelo sem dados do índice.
1. No painel de configurações, expanda o campo **Adicionar seus dados** e, em seguida, adicione o índice de projeto **brochures-index** e selecione o tipo de pesquisa **híbrido (vetor + palavra-chave)**.

   > **Dica**: em alguns casos, os índices recém-criados podem não estar disponíveis imediatamente. Atualizar o navegador geralmente ajuda, mas se você ainda estiver enfrentando o problema de não conseguir encontrar o índice, talvez seja necessário aguardar até que o índice seja reconhecido.

1. Depois que o índice tiver sido adicionado e a sessão de chat for reiniciada, reenvie o prompt `Where can I stay in New York?`
1. Revise a resposta, que deve ser baseada nos dados do índice.

## Criar um aplicativo cliente RAG com os SDKs da Fábrica de IA do Azure e do OpenAI do Azure

Agora que você tem um índice em funcionamento, pode usar os SDKs da Fábrica de IA do Azure e do OpenAI do Azure para implementar o padrão RAG em um aplicativo cliente. Vamos explorar o código para fazer isso em um exemplo simples.

> **Dica**: você pode optar por desenvolver sua solução RAG usando Python ou Microsoft C#. Siga as instruções na seção apropriada para o idioma escolhido.

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

1. No painel do PowerShell, insira os seguintes comandos para clonar o repositório GitHub que contém os arquivos de código deste exercício:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Dica**: ao colar comandos no Cloud Shell, a saída poderá ocupar uma grande quantidade do espaço da tela. Você pode limpar a tela digitando o comando `cls` para facilitar o foco em cada tarefa.

1. Após o repositório ser clonado, navegue até a pasta que contém os arquivos de código do aplicativo de chat:

    > **Observação**: siga as etapas para a linguagem de programação escolhida.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. No painel de linha de comando do Cloud Shell, digite o seguinte comando para instalar as bibliotecas que você usará:

    **Python**

    ```
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
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

1. No arquivo de código, substitua os seguintes espaços reservados: 
    - **your_project_connection_string**: substitua pela cadeia de conexão do projeto (copiada da página **Visão geral** do projeto no Portal da Fábrica de IA do Azure)
    - **your_model_deployment**: substitua pelo nome que você atribuiu à implantação de modelo (que deve ser `gpt-4`)
    - **your_index**: substitua pelo nome do índice (que deve ser `brochures-index`)
1. Depois de substituir os espaços reservados, use o comando **CTRL+S** ou **botão direito do mouse > Salvar** para salvar as suas alterações e, em seguida, use o comando **CTRL+Q** ou **botão direito do mouse > Sair** para fechar o editor de código, mantendo a linha de comando do Cloud Shell aberta.

### Explorar o código para implementar o padrão RAG

1. Digite o seguinte comando para editar o arquivo de código que foi fornecido:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Revise o código no arquivo, observando que:
    - Usa o SDK da Fábrica de IA do Azure para se conectar ao seu projeto (usando a cadeia de conexão do projeto)
    - Cria um cliente OpenAI do Azure autenticado com base na conexão do projeto.
    - Recupera a conexão padrão da Pesquisa de IA do Azure do seu projeto para que ele possa determinar o ponto de extremidade e a chave do serviço da Pesquisa de IA do Azure.
    - Cria uma mensagem de sistema adequada.
    - Envia um prompt (incluindo uma mensagem do sistema e do usuário com base na entrada do usuário) para o cliente do OpenAI do Azure, adicionando informações adicionais sobre o índice da Pesquisa de IA do Azure a ser usado para fundamentar o prompt.
    - Exibe a resposta do prompt fundamentado.
    - Adiciona a resposta ao histórico de chats.
1. Use o comando **CTRL+Q** para fechar o editor de código sem salvar alterações, enquanto mantém a linha de comando do Cloud Shell aberta.

### Executar o aplicativo de chat

1. No painel de linha de comando do Cloud Shell, insira o seguinte comando para executar o aplicativo:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Quando solicitado, insira uma pergunta, como `Where should I stay in London?` e analise a resposta do seu modelo de IA generativa.

    A resposta inclui referências de origem para indicar os dados indexados nos quais a resposta foi encontrada.

1. Tente uma pergunta de acompanhamento, por exemplo `What can I do there?`

1. Quando terminar, digite `quit` para sair do programa. Em seguida, feche o painel do Cloud Shell.

## Limpar

Para evitar custos desnecessários do Azure e utilização de recursos, você deve remover os recursos implantados neste exercício.

1. Se você terminou de explorar o Azure IA Foundry, retorne ao [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` e entre usando suas credenciais do Azure, se necessário. Em seguida, elimine os recursos no grupo de recursos onde aprovisionou os seus recursos IA do Azure Search e IA do Azure.
