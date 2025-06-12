---
lab:
  title: Crie um aplicativo de IA generativa que use seus próprios dados
  description: Saiba como usar o modelo RAG (Geração Aumentada de Recuperação) para criar um aplicativo de chat que fundamenta prompts usando seus próprios dados.
---

# Crie um aplicativo de IA generativa que use seus próprios dados

A Geração Aumentada de Recuperação (RAG) é uma técnica usada para compilar aplicativos que integram dados de fontes de dados personalizadas em um prompt para um modelo de IA generativa. O RAG é um padrão comumente usado para desenvolver aplicativos de IA generativa – aplicativos baseados em chat que usam um modelo de linguagem para interpretar entradas e gerar respostas apropriadas.

Neste exercício, você usará a Fábrica de IA do Azure para integrar dados personalizados em uma solução de IA generativa.

Este exercício levará, aproximadamente, **45** minutos.

> **Observação**: este exercício é serviços de pré-lançamento, que podem estar sujeitos a alterações.

## Criar um recurso da Fábrica de IA do Azure

Vamos começar criando um recurso da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [portal do Azure](https://portal.azure.com) em `https://portal.azure` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar.
1. Crie um recurso `Azure AI Foundry` com as seguintes configurações:
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Nome**: *um nome válido para o recurso da Fábrica de IA do Azure*
    - **Região**: escolha uma das seguintes regiões:
        - Leste dos EUA 2
        - Suécia Central
    - **Nome do projeto padrão**: *Um nome válido para o projeto*

1. Aguarde até que o recurso seja criado e vá para sua página no portal do Azure.
1. Na página do recurso da Fábrica de IA do Azure, selecione **Ir para o portal Fábrica de IA do Azure**.

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
1. No portal da Portal da Fábrica de IA do Azure, em seu projeto, no painel de navegação à esquerda, selecione **Playgrounds** e, em seguida, selecione **Experimentar o playground de chat**.
1. No painel **Configuração** do playground, expanda a seção **Adicionar seus dados** e selecione **Adicionar uma fonte de dados**.
1. No assistente **Adicionar dados**, expanda o menu suspenso para selecionar **Carregar arquivos**.
1. Crie um novo recurso do Armazenamento de Blobs do Azure com as seguintes configurações:
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *o mesmo grupo de recursos do recurso da Fábrica de IA do Azure*
    - **Nome da conta de armazenamento**: *um nome válido para o recurso da conta de armazenamento*
    - **Região**: *a mesma região do recurso da Fábrica de IA do Azure*
    - **Desempenho**: padrão
    - **Redundância**: LRS
1. Crie o recurso e aguarde até que a implantação seja concluída.
1. Retorne à guia Fábrica de IA do Azure, atualize a lista de recursos do armazenamento de Blobs do Azure e selecione a conta recém-criada.

    > **Observação**: se você receber um aviso de que o OpenAI do Azure precisa de sua permissão para acessar o recurso, selecione **Ativar CORS**.

1. Crie um novo recurso da Pesquisa de IA do Azure com as seguintes configurações:
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *o mesmo grupo de recursos do recurso da Fábrica de IA do Azure*
    - **Nome do serviço**: *um nome válido para o recurso da Pesquisa de IA do Azure*
    - **Região**: *a mesma região do recurso da Fábrica de IA do Azure*
    - **Tipo de preço**: Básico

1. Crie o recurso e aguarde até que a implantação seja concluída.
1. Retorne à guia Fábrica de IA do Azure, atualize a lista de recursos da Pesquisa de IA do Azure e selecione a conta recém-criada.
1. Nomeie seu índice `brochures-index`.
1. Habilite a opção **Adicionar busca em vetores a este recurso de pesquisa** e selecione o modelo de incorporação implantado anteriormente. Selecione **Avançar**.

   >**Observação**: pode demorar um pouco até que o assistente **Adicionar dados** reconheça seu modelo de incorporação implantado, portanto, se você não conseguir ativar a opção da busca em vetores, cancele o assistente, aguarde alguns minutos e tente novamente.

1. Carregue todos os arquivos .pdf da pasta **brochures** que você extraiu anteriormente e clique em **Avançar**.
1. Na etapa **Gerenciamento de dados**, selecione o tipo de busca **Híbrido (vetor + palavra-chave)** e o tamanho da parte de **1024**. Selecione **Avançar**.
1. Na etapa **Conexão de dados**, selecione **Chave de API** como o tipo de autenticação. Selecione **Avançar**.
1. Revise todas as etapas de configuração e clique em **Salvar e fechar**.
1. Aguarde a conclusão do processo de indexação, o que pode demorar um pouco, dependendo dos recursos de computação disponíveis em sua assinatura.

    > **Dica**: enquanto você espera a criação do índice, por que não dar uma olhada nos folhetos que você baixou para se familiarizar com o conteúdo?

## Testar o índice no playground

Antes de usar seu índice em um prompt flow baseado em RAG, vamos verificar se ele pode ser usado para afetar respostas de IA generativa.

1. Na página do playground Chat, no painel Configuração, verifique se a implantação do modelo **gpt-4o** está selecionada. Em seguida, no painel de sessão de chat principal, envie o prompt `Where can I stay in New York?`
1. Revise a resposta, que deve ser baseada nos dados do índice.

## Criar um aplicativo cliente RAG

Agora que você tem um índice em funcionamento, pode usar o SDK do OpenAI do Azure para implementar o padrão RAG em um aplicativo cliente. Vamos explorar o código para fazer isso em um exemplo simples.

> **Dica**: você pode optar por desenvolver sua solução RAG usando Python ou Microsoft C#. Siga as instruções na seção apropriada para o idioma escolhido.

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

    > **Observação**: siga as etapas para a linguagem de programação escolhida.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. No painel de linha de comando do Cloud Shell, digite o seguinte comando para instalar a biblioteca do SDK do OpenAI:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.OpenAI
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
    - **your_openai_endpoint**: o ponto de extremidade da OpenAI da página **Visão geral** do projeto no Portal da Fábrica de IA do Azure (selecione a guia da funcionalidade **OpenAI do Azure**, não a funcionalidade Inferência de IA do Azure ou Serviços de IA do Azure).
    - **your_openai_api_key**: a chave de API do OpenAI da página **Visão geral** do seu projeto no portal da Fábrica de IA do Azure (selecione a guia da funcionalidade **OpenAI do Azure**, não da funcionalidade Inferência de IA do Azure ou Serviços de IA do Azure).
    - **your_chat_model**: o nome que você atribuiu à implantação do modelo **gpt-4o** na página **Modelos + pontos de extremidade** no portal da Fábrica de IA do Azure (o nome padrão é `gpt-4o`).
    - **your_embedding_model**: o nome que você atribuiu à implantação do modelo **text-embedding-ada-002**, na página **Modelos + pontos de extremidade** no portal da Fábrica de IA do Azure (o nome padrão é `text-embedding-ada-002`).
    - **your_search_endpoint**: a URL do recurso Pesquisa de IA do Azure. Você a encontrará no **Centro de gerenciamento** no portal da Fábrica de IA do Azure.
    - **your_search_api_key**: a chave de API do recurso Pesquisa de IA do Azure. Você a encontrará no **Centro de gerenciamento** no portal da Fábrica de IA do Azure.
    - **your_index**: substitua pelo nome do índice na página **Dados + índices** do seu projeto no portal da Fábrica de IA do Azure (é `brochures-index`).
1. Após substituir os espaços reservados, no editor de código, use o comando **CTRL+S** ou **Clique com o botão direito > Salvar** para salvar as alterações e, em seguida, use o comando **CTRL+Q** ou **Clique com o botão direito > Sair** para fechar o editor de código, mantendo a linha de comando do Cloud Shell aberta.

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

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Quando solicitado, insira uma pergunta, como `Where should I go on vacation to see architecture?` e analise a resposta do seu modelo de IA generativa.

    A resposta inclui referências de origem para indicar os dados indexados nos quais a resposta foi encontrada.

1. Tente uma pergunta de acompanhamento, por exemplo `Where can I stay there?`

1. Quando terminar, digite `quit` para sair do programa. Em seguida, feche o painel do Cloud Shell.

## Limpar

Para evitar custos desnecessários do Azure e utilização de recursos, você deve remover os recursos implantados neste exercício.

1. Se você terminou de explorar o Azure IA Foundry, retorne ao [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` e entre usando suas credenciais do Azure, se necessário. Em seguida, elimine os recursos no grupo de recursos onde aprovisionou os seus recursos IA do Azure Search e IA do Azure.
