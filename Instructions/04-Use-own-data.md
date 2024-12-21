---
lab:
  title: Crie um aplicativo de IA generativa que use seus próprios dados
---

# Crie um aplicativo de IA generativa que use seus próprios dados

A Geração Aumentada de Recuperação (RAG) é uma técnica usada para compilar aplicativos que integram dados de fontes de dados personalizadas em um prompt para um modelo de IA generativa. O RAG é um padrão comumente usado para desenvolver aplicativos de IA generativa – aplicativos baseados em chat que usam um modelo de linguagem para interpretar entradas e gerar respostas apropriadas.

Neste exercício, você usará o portal do Azure IA Foundry para integrar dados personalizados em um prompt flow de IA generativa.

Este exercício levará, aproximadamente, **45** minutos.

## Criar um recurso do Azure AI Search

Sua solução de aplicativo de IA generativa integrará dados personalizados em um prompt flow. Para dar suporte a essa integração, você precisará de um recurso de Pesquisa de IA do Azure para indexar seus dados.

1. Em um navegador da Web, abra o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` e entre usando suas credenciais do Azure.
1. Na página inicial, selecione **+ Criar um recurso** e pesquise por `Azure AI Search`. Em seguida, crie um novo recurso de Pesquisa de IA do Azure com as seguintes configurações:

    - **Assinatura**: *Selecione sua assinatura do Azure*
    - **Grupo de recursos**: *selecione ou crie um grupo de recursos*.
    - **Nome do serviço**: *Insira um nome de serviço exclusivo*
    - **Localização**: *faça uma escolha **aleatória** de uma das regiões a seguir*\*
        - Leste da Austrália
        - Leste do Canadá
        - Leste dos EUA
        - Leste dos EUA 2
        - França Central
        - Leste do Japão
        - Centro-Norte dos EUA
        - Suécia Central
        - Suíça 
    - **Tipo de preço**: padrão

    > \* Posteriormente, você criará um Hub de IA do Azure (que inclui um serviço do OpenAI do Azure) na mesma região que o recurso da Pesquisa de IA do Azure. Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota em cenários em que você está compartilhando um locatário com outros usuários. No caso de um limite de cota ser atingido posteriormente no exercício, talvez seja necessário criar outro hub de IA do Azure em uma região diferente.

1. Aguarde a conclusão da implantação do recurso de Pesquisa de IA do Azure.

## Criar um projeto de IA do Azure

Agora você está pronto para criar um projeto do Azure IA Foundry e os recursos de IA do Azure para dar suporte a ele.

1. Em um navegador da Web, abra o [portal do Azure IA Foundry](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure.
1. Na home page, selecione **+Criar projeto**.
1. No assistente **Criar um projeto**, você pode ver todos os recursos do Azure que serão criados automaticamente com seu projeto. Selecione **Personalizar** e conecte-se ao recurso de pesquisa de IA do Azure:

    - **Nome do hub**: *Um nome exclusivo*
    - **Assinatura do Azure**: *sua assinatura do Azure*
    - **Grupo de recursos**: *Selecione o grupo de recursos que contém o recurso Pesquisa de IA do Azure*
    - **Localização**: *O mesmo local que o recurso da Pesquisa de IA do Azure*
    - **Conectar os Serviços de IA do Azure ou a OpenAI do Azure**: (novo) *Preenchimentos automáticos com o nome do hub selecionado*
    - **Conectar a Pesquisa de IA do Azure**: *selecione o recurso de Pesquisa de IA do Azure*

1. Clique em **Avançar** e revise a configuração.
1. Clique em **Criar** e aguarde a conclusão do processo.
   
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
      
1. Repita as etapas anteriores para implantar um modelo **gpt-35-turbo-16k** com o nome de implantação `gpt-35-turbo-16k`.

    > **Observação**: A redução dos Tokens por Minuto (TPM) ajuda a evitar o uso excessivo da cota disponível na assinatura que você estiver usando. 5.000 TPM são suficientes para os dados usados neste exercício.

## Adicionar dados ao seu projeto

Os dados para o seu copiloto consistem em um conjunto de folhetos de viagem em formato PDF da agência de viagens fictícia *Margie's Travel*. Vamos adicioná-los ao projeto.

1. Baixe o [arquivo compactado de folhetos](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) de `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` e extraia-o para uma pasta chamada **folhetos** em seu sistema de arquivos local.
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
        
1. Aguarde até que o processo de indexação seja concluído, o que pode levar vários minutos. A operação de criação de índice consiste nos seguintes trabalhos:

    - Extraia, particione e insira os tokens de texto nos dados de folhetos.
    - Crie o índice do IA do Azure Search.
    - Registrar o ativo de índice.

## Testar o índice

Antes de usar seu índice em um prompt flow baseado em RAG, vamos verificar se ele pode ser usado para afetar respostas de IA generativa.

1. No painel de navegação à esquerda, selecione a página **Playground**.
1. Na página Chat, no painel de instalação, verifique se a implantação de modelo **gpt-35-turbo-16k** está selecionada. Em seguida, no painel de sessão de chat principal, envie o prompt `Where can I stay in New York?`
1. Revise a resposta, que deve ser uma resposta genérica do modelo sem dados do índice.
1. No painel de configurações, expanda o campo **Adicionar seus dados** e, em seguida, adicione o índice de projeto **brochures-index** e selecione o tipo de pesquisa **híbrido (vetor + palavra-chave)**.

   > **Observação**: alguns usuários estão descobrindo que os índices recém-criados estão indisponíveis imediatamente. Atualizar o navegador geralmente ajuda, mas se você ainda estiver enfrentando o problema de não conseguir encontrar o índice, talvez seja necessário aguardar até que o índice seja reconhecido.

1. Depois que o índice tiver sido adicionado e a sessão de chat for reiniciada, reenvie o prompt `Where can I stay in New York?`
1. Revise a resposta, que deve ser baseada nos dados do índice.

## Use o índice em um prompt flow

Seu índice de vetor foi salvo em seu projeto do Azure IA Foundry, permitindo que você o use facilmente em um prompt flow.

1. No portal do Azure AI Foundry, em seu projeto, no painel de navegação à esquerda, em **Criar e personalizar**, selecione a página **Prompt flow**.
1. Crie um novo prompt flow clonando a amostra **Perguntas e respostas em várias rodadas sobre seus dados** na galeria. Salve seu clone desta amostra em uma pasta chamada `brochure-flow`.
    <details>  
      <summary><b>Dica de solução de problemas</b>: erro de permissões</summary>
        <p>Se você receber um erro de permissões ao criar um novo prompt flow, tente o seguinte para solucionar o problema:</p>
        <ul>
          <li>No portal do Azure, selecione o recurso Serviços de IA.</li>
          <li>Na guia Identidade, em Gerenciamento de recursos, confirme se é uma identidade gerenciada atribuída pelo sistema.</li>
          <li>Navegue até a conta de armazenamento associada. Na página do IAM, adicione a atribuição de função <em>Leitor de Dados do Blob de Armazenamento</em>.</li>
          <li>Em <strong>Atribuir acesso a</strong>, escolha <strong>Identidade Gerenciada</strong>, <strong>+ Selecionar membros</strong>, selecione <strong>Todas as identidades gerenciadas atribuídas pelo sistema</strong> e selecione o recurso dos Serviços de IA do Azure.</li>
          <li>Revise e atribua para salvar as novas configurações e repita a etapa anterior.</li>
        </ul>
    </details>

1. Quando a página do designer do prompt flow for aberta, revise **folheto-fluxo**. O grafo deve ser semelhante à seguinte imagem:

    ![Uma captura de tela de um gráfico de prompt flow](./media/chat-flow.png)

    O prompt flow de amostra que você está usando implementa a lógica de prompt para um aplicativo de chat no qual o usuário pode enviar iterativamente entrada de texto para a interface de chat. O histórico de conversação é retido e incluído no contexto de cada iteração. O prompt flow orquestra uma sequência de *ferramentas* para:

    - Anexar o histórico à entrada do bate-papo para definir um prompt na forma de uma pergunta contextualizada.
    - Recupere o contexto usando seu índice e um tipo de consulta de sua escolha com base na pergunta.
    - Gerar contexto de prompt usando os dados recuperados do índice para complementar a pergunta.
    - Criar variantes de prompt adicionando uma mensagem do sistema e estruturando o histórico de chat.
    - Enviar o prompt para um modelo de linguagem para gerar uma resposta de linguagem natural.

1. Use o botão **Iniciar a sessão de computação** para iniciar a computação do runtime no fluxo.

    Aguarde o runtime ser iniciado. Isso fornece um contexto de computação para o prompt flow. Enquanto aguarda, na guia **Fluxo**, revise as seções das ferramentas no fluxo.

1. Na seção **Entradas**, verifique se as entradas incluem:
    - **chat_history**
    - **chat_input**

    O histórico de chat padrão nesta amostra inclui algumas conversas sobre IA.

1. Na seção **Saídas**, verifique se a saída inclui:

    - **chat_output** com o valor ${chat_with_context.output}

1. Na seção **modify_query_with_history**, selecione as seguintes configurações (deixando as outras como estão):

    - **Conexão**: *O recurso OpenAI do Azure padrão no seu hub de IA*
    - **Api**: chat
    - **deployment_name**: gpt-35-turbo-16k
    - **response_format**: {"type":"text"}

1. Aguarde o início da sessão de computação e, na seção de **pesquisa**, defina os seguintes valores de parâmetro:

    - **mlindex_content**: *Selecione o campo vazio para abrir o painel Gerar*
        - **index_type**: Índice registrado
        - **mlindex_asset_id**: brochures-index:1
    - **queries**: ${modify_query_with_history.output}
    - **query_type**: Híbrido (vetor + palavra-chave)
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
    - **deployment_name**: gpt-35-turbo-16k
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

> **Observação**: dependendo da região e da carga do datacenter, as implantações às vezes podem demorar um pouco e, às vezes, gerar um erro ao interagir com a implantação. Fique à vontade para passar para a seção de desafio abaixo enquanto ela implanta ou ignora o teste de sua implantação se você tiver pouco tempo.

1. Na barra de ferramentas, selecione **Implantar**.
1. Crie uma implantação com as seguintes configurações:
    - **Configurações básicas**:
        - **Ponto de extremidade**: Novo
        - **Nome do ponto de extremidade**: *Use o nome de ponto de extremidade exclusivo padrão*
        - **Nome da implantação**: *Use o nome do ponto de extremidade de implantação padrão*
        - **Máquina virtual**: Standard_DS3_v2
        - **Contagem de instâncias**: 3
        - **Coleta de dados de inferência**: Selecionado
    - **Configurações avançadas**:
        - *Usar as configurações padrão*
1. No portal do Azure AI Foundry, em seu projeto, no painel de navegação à esquerda, em **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Continue atualizando a exibição até que a implantação de **brochure-endpoint-1** seja mostrada como *bem-sucedida* no ponto de extremidade **brochure-endpoint** (isso pode levar uma quantia significativa de tempo).
1. Quando a implantação for bem-sucedida, selecione-a. Em seguida, na página **Teste**, insira o prompt `What is there to do in San Francisco?` e revise a resposta.
1. Insira o prompt `Where else could I go?` e revise a resposta.
1. Exiba a página **Consumir** para o ponto de extremidade e observe que ele contém informações de conexão e código de exemplo que você pode usar para compilar um aplicativo cliente para o ponto de extremidade, permitindo que você integre a solução de prompt flow a um aplicativo como um copiloto personalizado.

## Desafio 

Agora que você experimentou como integrar seus próprios dados em um aplicativo de IA generativa criado com o portal do Azure IA Foundry, vamos explorar mais!

Tente adicionar uma nova fonte de dados por meio do portal do Azure IA Foundry, indexá-la e integrar os dados indexados em um prompt flow. Estes são alguns conjuntos de dados que você pode tentar:

- Uma coleção de artigos (de pesquisa) que você tem em seu computador.
- Um conjunto de apresentações de conferências anteriores.
- Qualquer um dos conjuntos de dados disponíveis no repositório de [dados de amostra do Azure Search](https://github.com/Azure-Samples/azure-search-sample-data).

Use o máximo de recursos que puder para criar sua fonte de dados e integrá-la ao prompt flow. Experimente o novo prompt flow e envie prompts que só poderiam ser respondidos pelo conjunto de dados que você escolheu!

## Limpar

Para evitar custos desnecessários do Azure e utilização de recursos, você deve remover os recursos implantados neste exercício.

1. Se você terminou de explorar o Azure IA Foundry, retorne ao [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` e entre usando suas credenciais do Azure, se necessário. Em seguida, elimine os recursos no grupo de recursos onde aprovisionou os seus recursos IA do Azure Search e IA do Azure.
