---
lab:
  title: Crie um copiloto personalizado que use seus próprios dados
---

# Crie um copiloto personalizado que use seus próprios dados

A Geração Aumentada de Recuperação (RAG) é uma técnica usada para compilar aplicativos que integram dados de fontes de dados personalizadas em um prompt para um modelo de IA generativa. O RAG é um padrão comumente usado para desenvolver *copilotos* personalizados – aplicativos baseados em chat que usam um modelo de linguagem para interpretar entradas e gerar respostas apropriadas.

Neste exercício, você usará o Estúdio de IA do Azure para integrar dados personalizados em um prompt flow de IA generativa.

> **Observação**: O Estúdio de IA do Azure está em versão prévia no momento em que este artigo foi escrito e sob desenvolvimento ativo. Alguns elementos do serviço podem não ser exatamente como descritos, e alguns recursos podem não funcionar como esperado.

Este exercício levará, aproximadamente, **45** minutos.

## Criar um recurso do Azure AI Search

Sua solução de copiloto integrará dados personalizados em um prompt flow. Para dar suporte a essa integração, você precisará de um recurso de Pesquisa de IA do Azure para indexar seus dados.

1. Em um navegador da Web, abra o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` e entre usando suas credenciais do Azure.
1. Na página inicial, selecione **+ Criar um recurso** e pesquise por `Azure AI Search`. Em seguida, crie um novo recurso de Pesquisa de IA do Azure com as seguintes configurações:

    - **Assinatura**: *Selecione sua assinatura do Azure*
    - **Grupo de recursos**: *selecione ou crie um grupo de recursos*.
    - **Nome do serviço**: *Insira um nome de serviço exclusivo*
    - **Localização**: *Selecione qualquer local disponível*
    - **Tipo de preço**: padrão

1. Aguarde a conclusão da implantação do recurso de Pesquisa de IA do Azure.

## Criar um projeto de IA do Azure

Agora você está pronto para criar um projeto do Estúdio de IA do Azure e os recursos de IA do Azure para dar suporte a ele.

1. Em um navegador da Web, abra o [Estúdio de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure.
1. Na página **Compilar**, selecione **+ Novo projeto de IA**. Em seguida, no assistente **Introdução**, crie um projeto com as seguintes configurações:

    - **Nome do projeto**: *Um nome exclusivo para seu projeto*
    - **Hub de IA**: *Crie um novo recurso com as seguintes configurações:*

        - **Nome do Hub de IA**: *Um nome exclusivo*
        - **Assinatura do Azure**: *sua assinatura do Azure*
        - **Grupo de recursos**: *Selecione o grupo de recursos que contém o recurso Pesquisa de IA do Azure*
        - **Localização**: *O mesmo local que o recurso de Pesquisa de IA do Azure (ou um local geograficamente próximo a ele)*
        - **OpenAI do Azure**: (Novo) *Preenchimentos automáticos com o nome do hub selecionado*
        - **Pesquisa de IA do Azure**: *Selecione o recurso de Pesquisa de IA do Azure*

1. Aguarde até que seu projeto seja criado.

## Implantar modelos

Você precisará de dois modelos para implementar sua solução:

- Um modelo de *inserção* para vetorizar dados de texto para indexação e processamento eficientes.
- Um modelo que pode gerar respostas em linguagem natural para perguntas com base em seus dados.

1. No Estúdio de IA do Azure, em seu projeto, no painel de navegação à esquerda, em **Componentes**, selecione a página **Implantações**.
1. Crie uma nova implantação (usando um **ponto de extremidade em tempo real**) do modelo **text-embedding-ada-002** com as seguintes configurações:

    - **Nome da implantação**: `text-embedding-ada-002`
    - **Versão do modelo**: *Padrão*
    - **Opções avançadas**:
        - **Filtro de conteúdo**: *Padrão*
        - **Limite de taxa de tokens por minuto**: `5K`
1. Repita as etapas anteriores para implantar um modelo **gpt-35-turbo** com o nome de implantação `gpt-35-turbo`.

    > **Observação**: A redução dos Tokens por Minuto (TPM) ajuda a evitar o uso excessivo da cota disponível na assinatura que você estiver usando. 5.000 TPM são suficientes para os dados usados neste exercício.

## Adicionar dados ao seu projeto

Os dados para o seu copiloto consistem em um conjunto de folhetos de viagem em formato PDF da agência de viagens fictícia *Margie's Travel*. Vamos adicioná-los ao projeto.

1. Baixe o [arquivo compactado de folhetos](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) de `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` e extraia-o para uma pasta chamada **folhetos** em seu sistema de arquivos local.
1. No Estúdio de IA do Azure, em seu projeto, no painel de navegação à esquerda, em **Componentes**, selecione a página **Dados**.
1. Selecione **+ Novos dados**.
1. No assistente **Adicionar dados**, expanda o menu suspenso para selecionar **Carregar arquivos/pastas**.
1. Selecione **Carregar pasta** e selecione a pasta **folhetos**.
1. Defina o nome dos dados como **folhetos**.

## Crie um índice para seus dados

Agora que você adicionou uma fonte de dados ao seu projeto, pode usá-la para criar um índice em seu recurso de Pesquisa de IA do Azure.

1. No Estúdio de IA do Azure, em seu projeto, no painel de navegação à esquerda, em **Componentes**, selecione a página **Índices**.
1. Adicione um novo índice com as seguintes configurações:
    - **Dados de origem**:
        - **Fonte de dados**: Usar dados de projeto existentes
            - *Selecione a fonte de dados dos **folhetos***
    - **Armazenamento de índice**:
        - *Selecione a conexão do **AzureAISearch** com seu recurso de Pesquisa de IA do Azure*
    - **Configurações de pesquisa**:
        - **Configurações de vetor**: Adicione busca em vetores a este recurso de pesquisa
        - **Recursos do OpenAI do Azure**: Default_AzureOpenAI
        - *Reconheça que um modelo de inserção será implantado se ainda não estiver presente*
    - **Configurações de índice**:
        - **Nome do índice**: folhetos-índice
        - **Máquina virtual**: Selecionar automaticamente
1. Aguarde até que o processo de indexação seja concluído, o que pode levar vários minutos. A operação de criação de índice consiste nos seguintes trabalhos:

    - Extraia, particione e insira os tokens de texto nos dados de folhetos.
    - Atualize a Pesquisa de IA do Azure com o novo índice.
    - Registrar o ativo de índice.

## Testar o índice

Antes de usar seu índice em um prompt flow baseado em RAG, vamos verificar se ele pode ser usado para afetar respostas de IA generativa.

1. No painel de navegação à esquerda, em **Ferramentas**, selecione a página **Playground**.
1. Na página Playground, no painel Opções, certifique-se de que a implantação do modelo **gpt-35-turbo** esteja selecionada. Em seguida, no painel de sessão de chat principal, envie o prompt `Where can I stay in New York?`
1. Revise a resposta, que deve ser uma resposta genérica do modelo sem dados do índice.
1. No painel de instalação, selecione a guia **Adicionar seus dados** e, em seguida, adicione o índice de projeto **brochures-index** e selecione o tipo de pesquisa **híbrido (vetor + palavra-chave)**.
1. Depois que o índice tiver sido adicionado e a sessão de chat for reiniciada, reenvie o prompt `Where can I stay in New York?`
1. Revise a resposta, que deve ser baseada nos dados do índice.

## Use o índice em um prompt flow

Seu índice de vetor foi salvo em seu projeto do Estúdio de IA do Azure, permitindo que você o use facilmente em um prompt flow.

1. No Estúdio de IA do Azure, em seu projeto, no painel de navegação à esquerda, em **Ferramentas**, selecione a página **Prompt flow**.
1. Crie um novo prompt flow clonando a amostra **Perguntas e respostas em várias rodadas sobre seus dados** na galeria. Salve seu clone desta amostra em uma pasta chamada `brochure-flow`.
1. Quando a página do designer do prompt flow for aberta, revise **folheto-fluxo**. O grafo deve ser semelhante à seguinte imagem:

    ![Uma captura de tela de um gráfico de prompt flow](./media/chat-flow.png)

    O prompt flow de amostra que você está usando implementa a lógica de prompt para um aplicativo de chat no qual o usuário pode enviar iterativamente entrada de texto para a interface de chat. O histórico de conversação é retido e incluído no contexto de cada iteração. O prompt flow orquestra uma sequência de *ferramentas* para:

    - Anexar o histórico à entrada do bate-papo para definir um prompt na forma de uma pergunta contextualizada.
    - Recupere o contexto usando seu índice e um tipo de consulta de sua escolha com base na pergunta.
    - Gerar contexto de prompt usando os dados recuperados do índice para complementar a pergunta.
    - Criar variantes de prompt adicionando uma mensagem do sistema e estruturando o histórico de chat.
    - Enviar o prompt para um modelo de linguagem para gerar uma resposta de linguagem natural.

1. Na lista **Runtime**, selecione **Iniciar** para iniciar o runtime automático.

    Aguarde o runtime ser iniciado. Isso fornece um contexto de computação para o prompt flow. Enquanto aguarda, na guia **Fluxo**, revise as seções das ferramentas no fluxo.

1. Na seção **Entradas**, verifique se as entradas incluem:
    - **chat_history**
    - **chat_input**

    O histórico de chat padrão nesta amostra inclui algumas conversas sobre IA.

1. Na seção **Saídas**, verifique se a saída inclui:

    - **chat_output** com valor `${chat_with_context.output}`

1. Na seção **modify_query_with_history**, selecione as seguintes configurações (deixando as outras como estão):

    - **Conexão**: `Default_AzureOpenAI`
    - **Api**: `chat`
    - **deployment_name**: `gpt-35-turbo`
    - **response_format**: `{"type":"text"}`

1. Na seção de **pesquisa**, defina os seguintes valores de parâmetro:

    - **mlindex_content**: *Selecione o campo vazio para abrir o painel Gerar*
        - **index_type**: Índice registrado
        - **mlindex_asset_id**: brochures-index:1
    - **consultas**: `${modify_query_with_history.output}`
    - **query_type**: `Hybrid (vector + keyword)`
    - **top_k**: 2

1. Na seção **generate_prompt_context**, revise o script Python e verifique se as **entradas** para esta ferramenta incluem o seguinte parâmetro:

    - **search_result** *(object)*: ${lookup.output}

1. Na seção **Prompt_variants**, revise o script Python e verifique se as **entradas** para esta ferramenta incluem os seguintes parâmetros:

    - **contextos** *(cadeia de caracteres)*: ${generate_prompt_context.output}
    - **chat_history** *(cadeia de caracteres)*: ${inputs.chat_history}
    - **chat_input** *(cadeia de caracteres)*: ${inputs.chat_input}

1. Na seção **chat_with_context**, selecione as seguintes configurações (deixando as outras como estão):

    - **Conexão**: Default_AzureOpenAI
    - **Api**: Chat
    - **deployment_name**: gpt-35-turbo
    - **response_format**: {"type":"text"}

    Em seguida, verifique se as **entradas** para esta ferramenta incluem os seguintes parâmetros:
    - **prompt_text** *(cadeia de caracteres)*: ${Prompt_variants.output}

1. Na barra de ferramentas, use o botão **Salvar** para salvar as alterações feitas nas ferramentas no prompt flow.
1. Na barra de ferramentas, selecione **Chat**. Um painel de chat é aberto com o histórico de conversa de amostra e a entrada já preenchida com base nos valores de amostra. Você pode ignorá-los.
1. No painel de chat, substitua a entrada padrão pela pergunta `Where can I stay in London?` e envie-a.
1. Revise a resposta, que deve ser baseada nos dados do índice.
1. Revise as saídas para cada ferramenta no fluxo.
1. No painel de chat, insira a pergunta `What can I do there?`
1. Revise a resposta, que deve ser baseada em dados no índice e levar em conta o histórico de chat (para que "lá" seja entendido como "em Londres").
1. Revise as saídas para cada ferramenta no fluxo, observando como cada ferramenta no fluxo operou em suas entradas para preparar um prompt contextualizado e obter uma resposta apropriada.

## Implantar o fluxo

Agora que você tem um fluxo de trabalho que usa seus dados indexados, pode implantá-lo como um serviço a ser consumido por um aplicativo copiloto.

1. Na barra de ferramentas, selecione **Implantar**.
1. Crie uma implantação com as seguintes configurações:
    - **Configurações básicas**:
        - **Ponto de extremidade**: Novo
        - **Nome do ponto de extremidade**: `brochure-endpoint`
        - **Nome da implantação**: folheto-ponto de extremidade-1
        - **Máquina virtual**: Standard_DS3_v2
        - **Contagem de instâncias**: 3
        - **Coleta de dados de inferência**: Selecionado
    - **Configurações avançadas**:
        - *Usar as configurações padrão*
1. No Estúdio de IA do Azure, em seu projeto, no painel de navegação à esquerda, em **Componentes**, selecione a página **Implantações**.
1. Continue atualizando a exibição até que a implantação de **brochure-endpoint-1** seja mostrada como *bem-sucedida* no ponto de extremidade **brochure-endpoint** (isso pode levar uma quantia significativa de tempo).
1. Quando a implantação for bem-sucedida, selecione-a. Em seguida, na página **Teste**, insira o prompt `What is there to do in San Francisco?` e revise a resposta.
1. Insira o prompt `Where else could I go?` e revise a resposta.
1. Exiba a página **Consumir** para o ponto de extremidade e observe que ele contém informações de conexão e código de exemplo que você pode usar para compilar um aplicativo cliente para o ponto de extremidade, permitindo que você integre a solução de prompt flow a um aplicativo como um copiloto personalizado.

## Limpeza

Para evitar custos desnecessários do Azure e utilização de recursos, você deve remover os recursos implantados neste exercício.

1. No Estúdio de IA do Azure, exiba a página **Compilar**. Em seguida, selecione o projeto criado neste exercício e use o botão **Excluir projeto** para removê-lo. Pode levar alguns minutos para excluir todos os componentes.
1. Se você terminou de explorar o Estúdio de IA do Azure, retorne ao [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` e entre usando suas credenciais do Azure, se necessário. Em seguida, exclua o grupo de recursos que você criou para seus recursos de Pesquisa de IA do Azure e IA do Azure.
