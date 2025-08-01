---
lab:
  title: Crie um aplicativo de IA generativa que use seus próprios dados
  description: Saiba como usar o modelo RAG (Geração Aumentada de Recuperação) para criar um aplicativo de chat que fundamenta prompts usando seus próprios dados.
---

# Crie um aplicativo de IA generativa que use seus próprios dados

A Geração Aumentada de Recuperação (RAG) é uma técnica usada para compilar aplicativos que integram dados de fontes de dados personalizadas em um prompt para um modelo de IA generativa. O RAG é um padrão comumente usado para desenvolver aplicativos de IA generativa – aplicativos baseados em chat que usam um modelo de linguagem para interpretar entradas e gerar respostas apropriadas.

Neste exercício, você usará a Fábrica de IA do Azure para integrar dados personalizados em uma solução de IA generativa.

> **Observação**: o código neste exercício se baseia no software SDK de pré-lançamento, que pode estar sujeito a alterações. Quando necessário, usamos versões específicas de pacotes que podem não refletir as versões mais recentes disponíveis. Você pode experimentar algum comportamento inesperado, avisos ou erros.

Embora este exercício seja baseado no SDK do Python do OpenAI do Azure, você pode desenvolver aplicativos de chat de IA usando vários SDKs específicos de linguagem; incluindo:

- [OpenAI para Python](https://pypi.org/project/openai/)
- [Open IA do Azure para Microsoft .NET](https://www.nuget.org/packages/Azure.AI.OpenAI)
- [OpenAI do Azure para TypeScript](https://www.npmjs.com/package/@azure/openai)

Este exercício levará, aproximadamente, **45** minutos.

## Crie um hub e projeto da Fábrica de IA do Azure

Os recursos da Fábrica de IA do Azure que usaremos neste exercício requerem um projeto baseado em um recurso de *hub* da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir (feche o painel **Ajuda** se estiver aberto):

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. No navegador, navegue até `https://ai.azure.com/managementCenter/allResources` e clique em **Criar**. Em seguida, escolha a opção para criar um novo **Recurso do hub de IA**.
1. No assistente **Criar projeto**, insira um nome válido para o projeto e selecione a opção para criar um novo hub. Em seguida, use o link **Renomear hub** para especificar um nome válido para o novo hub, expanda **Opções avançadas** e especifique as seguintes configurações para o projeto:
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Região**: Leste dos EUA 2 ou Suécia Central (*Caso um limite de cota seja excedido posteriormente no exercício, talvez você precise criar outro recurso em uma região diferente.*)

    > **Observação**: se você estiver trabalhando em uma assinatura do Azure na qual as políticas são usadas para restringir nomes de recursos permitidos, talvez seja necessário usar o link na parte inferior da caixa de diálogo **Criar um novo projeto** para criar o hub usando o portal do Azure.

    > **Dica**: se o botão **Criar** ainda estiver desativado, certifique-se de renomear o hub com um valor alfanumérico exclusivo.

1. Aguarde a criação do projeto e navegue até o projeto.

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
            - **Assinatura**: *sua assinatura do Azure*
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

## Criar um aplicativo cliente RAG

Agora que você tem um índice em funcionamento, pode usar o SDK do OpenAI do Azure para implementar o padrão RAG em um aplicativo cliente. Vamos explorar o código para fazer isso em um exemplo simples.

### Preparar a configuração de aplicativo

1. Retorne à guia do navegador que contém o portal do Azure (mantendo o portal da Portal da Fábrica de IA do Azure aberto na guia existente).
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

    > **Dica**: ao colar comandos no Cloud Shell, a saída poderá ocupar uma grande quantidade do espaço da tela. Você pode limpar a tela digitando o comando `cls` para facilitar o foco em cada tarefa.

1. Após o repositório ser clonado, navegue até a pasta que contém os arquivos de código do aplicativo de chat:

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

1. No painel de linha de comando do Cloud Shell, digite o seguinte comando para instalar a biblioteca do SDK do OpenAI:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt openai
    ```

1. Digite o seguinte comando para editar o arquivo de configuração que foi fornecido:

    ```
   code .env
    ```

    O arquivo é aberto em um editor de código.

1. No arquivo de configuração, substitua os seguintes espaços reservados: 
    - **your_openai_endpoint**: O ponto de extremidade OpenAI da página **Visão Geral** do seu projeto no portal da Fábrica de IA do Azure (certifique-se de selecionar a guia de capacidade do **OpenAI do Azure**, e não a capacidade de Inferência de IA do Azure ou dos Serviços de IA do Azure).
    - **your_openai_api_key** A chave de API Open AI da página **Visão Geral** do seu projeto no portal da Fábrica de IA do Azure (certifique-se de selecionar a guia de capacidade do **OpenAI do Azure**, e não a capacidade de Inferência de IA do Azure ou dos Serviços de IA do Azure).
    - **your_chat_model**: o nome que você atribuiu à implantação do modelo **gpt-4o** na página **Modelos + pontos de extremidade** no portal da Fábrica de IA do Azure (o nome padrão é `gpt-4o`).
    - **your_embedding_model**: o nome que você atribuiu à implantação do modelo **text-embedding-ada-002**, na página **Modelos + pontos de extremidade** no portal da Fábrica de IA do Azure (o nome padrão é `text-embedding-ada-002`).
    - **your_search_endpoint**: a URL do recurso Pesquisa de IA do Azure. Você a encontrará no **Centro de gerenciamento** no portal da Fábrica de IA do Azure.
    - **your_search_api_key**: a chave de API do recurso Pesquisa de IA do Azure. Você a encontrará no **Centro de gerenciamento** no portal da Fábrica de IA do Azure.
    - **your_index**: substitua pelo nome do índice na página **Dados + índices** do seu projeto no portal da Fábrica de IA do Azure (é `brochures-index`).
1. Após substituir os espaços reservados, no editor de código, use o comando **CTRL+S** ou **Clique com o botão direito > Salvar** para salvar as alterações e, em seguida, use o comando **CTRL+Q** ou **Clique com o botão direito > Sair** para fechar o editor de código, mantendo a linha de comando do Cloud Shell aberta.

### Explorar o código para implementar o padrão RAG

1. Digite o seguinte comando para editar o arquivo de código que foi fornecido:

    ```
   code rag-app.py
    ```

1. Revise o código no arquivo, observando que:
    - Ele cria um cliente OpenAI do Azure usando o ponto de extremidade, a chave e o modelo de chat.
    - Ele cria uma mensagem de sistema adequada para uma solução de chat relacionada a viagens.
    - Envia um prompt (incluindo uma mensagem do sistema e do usuário com base na entrada do usuário) para o cliente do OpenAI do Azure, adicionando:
        - Informações da conexão para o índice da Pesquisa de IA do Azure a ser consultado.
        - Detalhes do modelo de inserção a ser usado para vetorizar a consulta\*.
    - Exibe a resposta do prompt fundamentado.
    - Adiciona a resposta ao histórico de chats.

    \**A consulta para o índice de pesquisa é baseada no prompt e é usada para localizar texto relevante nos documentos indexados. Você pode usar uma pesquisa baseada em palavras-chave que envia a consulta como texto, mas usar uma pesquisa baseada em vetor pode ser mais eficiente, daí o uso de um modelo de incorporação para vetorizar o texto da consulta antes de enviá-lo.*

1. Use o comando **CTRL+Q** para fechar o editor de código sem salvar alterações, enquanto mantém a linha de comando do Cloud Shell aberta.

### Executar o aplicativo de chat

1. No painel de linha de comando do Cloud Shell, insira o seguinte comando para executar o aplicativo:

    ```
   python rag-app.py
    ```

1. Quando solicitado, insira uma pergunta, como `Where should I go on vacation to see architecture?` e analise a resposta do seu modelo de IA generativa.

    A resposta inclui referências de origem para indicar os dados indexados nos quais a resposta foi encontrada.

1. Tente uma pergunta de acompanhamento, por exemplo `Where can I stay there?`

1. Quando terminar, digite `quit` para sair do programa. Em seguida, feche o painel do Cloud Shell.

## Limpar

Para evitar custos desnecessários do Azure e utilização de recursos, você deve remover os recursos implantados neste exercício.

1. Se você terminou de explorar o Azure IA Foundry, retorne ao [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` e entre usando suas credenciais do Azure, se necessário. Em seguida, elimine os recursos no grupo de recursos onde aprovisionou os seus recursos IA do Azure Search e IA do Azure.
