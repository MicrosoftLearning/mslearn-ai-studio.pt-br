---
lab:
  title: 'Explorar, implantar e conversar com modelos de linguagem no Estúdio de IA do Azure'
---

# Explorar, implantar e conversar com modelos de linguagem no Estúdio de IA do Azure

O catálogo de modelos do Estúdio de IA do Azure serve como um repositório central onde você pode explorar e usar uma variedade de modelos, facilitando a criação do cenário de IA generativa.

Neste exercício, você explorará o catálogo de modelos no Estúdio de IA do Azure.

Este exercício levará aproximadamente **25** minutos.

## Crie um hub de IA do Azure

Você precisa de um hub IA do Azure na sua assinatura do Azure para hospedar projetos. É possível criar esse recurso ao criar um projeto ou provisioná-lo com antecedência (que é o que faremos neste exercício).

1. Na seção **Gerenciamento**, selecione **Todos os recursos** e **+ Novo hub**. Crie um novo hub com as seguintes configurações:
    - **Nome do hub**: *Um nome exclusivo*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *Um novo grupo de recursos*
    - **Local**: Selecione **Ajude-me a escolher** e, em seguida, selecione **gpt-35-turbo** na janela Auxiliar de local e use a região recomendada\*
    - **Conecte os Serviços de IA do Azure ou do OpenAI do Azure**: *Crie uma nova conexão*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas no auxiliar de localização incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente. Saiba mais sobre a [disponibilidade do modelo por região](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

    Após a criação do hub IA do Azure, ele deverá ser semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um hub de IA do Azure no Azure AI Studio.](./media/azure-ai-resource.png)

1. Abra uma nova guia do navegador (deixando a guia do Estúdio de IA do Azure aberta) e navegue até o portal do Azure em [https://portal.azure.com](https://portal.azure.com?azure-portal=true), entrando com suas credenciais do Azure, se solicitado.
1. Navegue até ao grupo de recursos onde criou o seu hub IA do Azure e veja os recursos Azure que foram criados.

    ![Captura de tela de um hub de IA do Azure e recursos relacionados no portal do Azure.](./media/azure-portal.png)

1. Retorne à guia do navegador do Estúdio de IA do Azure.
1. Veja cada uma das páginas no painel do lado esquerdo da página do seu hub IA do Azure e observe os artefatos que pode criar e gerir. Na página **Conexões**, observe que as conexões com os serviços Azure OpenAI e AI já foram criadas.

## Criar um projeto

Um hub IA do Azure fornece um espaço de trabalho colaborativo no qual você pode definir um ou mais *projetos*. Vamos criar um projeto no seu hub IA do Azure.

1. Na IA do Azure Studio, verifique se você está no hub que acabou de criar (você pode verificar sua localização verificando o caminho na parte superior da tela).
1. Navegue até **Todos os projetos** usando o menu à esquerda.
1. Selecione **+ New project**.
1. No assistente **Criar um projeto**, crie um projeto com as seguintes configurações:
    - **Central atual**: *Seu hub de IA*
    - **Nome do projeto**: *Um nome exclusivo para seu projeto*
1. Aguarde até que seu projeto seja criado. O resultado deve ser similar à imagem a seguir:

    ![Captura de tela de uma página de detalhes do projeto no Estúdio de IA do Azure.](./media/azure-ai-project.png)

1. Exiba as páginas no painel do lado esquerdo, expandindo cada seção, e observe as tarefas que você pode executar e os recursos que você pode gerenciar em um projeto.

## Escolher um modelo usando parâmetros de comparação de modelo

Antes de implantar um modelo, você pode explorar os parâmetros de comparação de modelo para decidir qual modelo melhor atende às suas necessidades.

Imagine que você quer criar um copiloto personalizado que serve como assistente de viagem. Especificamente, você quer que seu copiloto ofereça suporte para consultas relacionadas a viagens, como requisitos de visto, previsões do tempo, atrações locais e normas culturais.

Seu copiloto precisará fornecer informações factualmente precisas, portanto, o embasamento é importante. Além disso, você quer que as respostas do copiloto sejam fáceis de ler e entender. Portanto, você também quer escolher um modelo com alto índice de fluência e coerência.

1. No Estúdio de IA do Azure, navegue até **Parâmetros de comparação de modelo** na seção **Introdução**, usando o menu à esquerda.
    Na aba **Parâmetros de comparação de qualidade**, você pode encontrar alguns gráficos já visualizados para você, comparando diferentes modelos.
1. Filtre os modelos mostrados:
    - **Tarefas**: respostas às perguntas
    - **Coleções**: OpenAI do Azure
    - **Métricas**: coerência, fluência, embasamento
1. Explore os gráficos resultantes e a tabela de comparação. Ao explorar, você pode tentar responder às seguintes perguntas:
    - Você percebe uma diferença de desempenho entre os modelos GPT-3.5 e GPT-4?
    - Existe alguma diferença entre versões do mesmo modelo?
    - Como as variantes de 32k diferem dos modelos básicos?

Na coleção OpenAI do Azure, você pode escolher entre os modelos GPT-3.5 e GPT-4. Vamos implantar esses dois modelos e explorar como eles se comparam para seu caso de uso.

## Implantar modelos OpenAI do Azure

Agora que explorou suas opções usando parâmetros de comparação de modelo, você já pode implantar modelos de linguagem. Você pode navegar pelo catálogo de modelos e implantar a partir daí ou pode implantar um modelo na página **Implantações**. Vamos explorar as duas opções.

### Implantar um modelo do Catálogo de modelos

Vamos começar implantando um modelo do catálogo de modelos. Você pode preferir essa opção quando quiser filtrar todos os modelos disponíveis.

1. Navegue até a página **Catálogo de modelos** na seção **Introdução**, usando o menu à esquerda.
1. Pesquise e implante o modelo `gpt-35-turbo`, selecionado pela IA do Azure, com as seguintes configurações:
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Tipo de implantação**: Padrão
    - **Versão do modelo**: *Selecione a versão padrão*
    - **Recurso de IA**: *escolha o recurso criado anteriormente*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: DefaultV2
    - **Habilitar cota dinâmica**: Desabilitado

### Implantar um modelo por meio de Implantações

Se você já sabe exatamente qual modelo quer implantar, talvez prefira fazê-lo por meio de Implantações.

1. Navegue até a página **Implantações** na seção **Componentes** usando o menu à esquerda.
1. Na guia **Implantações de modelo**, crie uma implantação com as seguintes configurações:
    - **Modelo**: gpt-4
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Tipo de implantação**: Padrão
    - **Versão do modelo**: *Selecione a versão padrão*
    - **Recurso de IA**: *escolha o recurso criado anteriormente*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: DefaultV2
    - **Habilitar cota dinâmica**: Desabilitado

    > **Observação**: você deve ter notado alguns modelos mostrando os parâmetros de comparação de modelos, mas não como uma opção em seu catálogo de modelos. A disponibilidade do modelo difere de acordo com o local. Seu local é especificado no nível do hub de IA, onde você pode usar o **Auxiliar de local** para especificar o modelo que você quer implantar para obter uma lista de locais em que você pode implantá-lo.

## Testar modelos no playground de chat

Agora que temos dois modelos para comparar, vamos ver como os modelos se comportam em uma interação de conversa.

1. Navegue até a página **Chat** na seção **Playground do projeto**, usando o menu à esquerda.
1. No **Playground de chat**, selecione sua implantação GPT-3.5.
1. Na janela de chat, insira a consulta `What can you do?` e veja a resposta:
    As respostas são muito genéricas. Lembre-se de que queremos criar um copiloto personalizado que sirva como assistente de viagem. Você pode especificar o tipo de ajuda que quer na pergunta que fizer.
1. Na janela de chat, insira a consulta `Imagine you're a travel assistant, what can you help me with?` As respostas já são mais específicas. Você pode não querer que seus usuários finais tenham que fornecer o contexto necessário toda vez que interagirem com seu copiloto. Para adicionar instruções globais, você pode editar a mensagem do sistema.
1. Atualize a mensagem do sistema com o seguinte prompt:

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Clique em **Salvar** e **Limpar chat**.
1. Na janela de chat, insira a consulta `What can you do?` e exiba a nova resposta. Observe como é diferente da resposta que você recebeu antes. A resposta é específica para viagem agora.
1. Continue a conversa perguntando: `I'm planning a trip to London, what can I do there?` O copiloto oferece muitas informações relacionadas à viagem. Você pode querer melhorar a saída ainda. Por exemplo, você pode querer que a resposta seja mais sucinta.
1. Atualize a mensagem do sistema adicionando `Answer with a maximum of two sentences.` ao final da mensagem. Aplique a alteração, limpe o chat e teste-o novamente perguntando: `I'm planning a trip to London, what can I do there?` Você também pode querer que seu copiloto continue a conversa em vez de simplesmente responder à pergunta.
1. Atualize a mensagem do sistema adicionando `End your answer with a follow-up question.` ao final da mensagem. Salve a alteração, limpe o chat e teste-o novamente perguntando: `I'm planning a trip to London, what can I do there?`
1. Altere a **implantação** para seu modelo GPT-4 e repita todas as etapas desta seção. Observe como os modelos podem ter saídas diferentes.
1. Por fim, teste os dois modelos na consulta `Who is the prime minister of the UK?`. O desempenho nesta questão está relacionado à fundamentação (se a resposta é factualmente precisa) dos modelos. O desempenho se correlaciona com suas conclusões dos parâmetros de comparação dos modelos?

Agora que você explorou os dois modelos, considere qual modelo você escolheria agora para seu caso de uso. A princípio, as saídas dos modelos podem ser diferentes e você pode preferir um modelo ao outro. No entanto, depois de atualizar a mensagem do sistema, você pode notar que a diferença é mínima. Do ponto de vista da otimização de custos, você pode optar pelo modelo GPT-3.5 em vez do modelo GPT-4, pois o desempenho dos dois é muito semelhante.

## Limpar

Se tiver terminado de explorar o Estúdio de IA do Azure, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Retorne à guia do navegador que contém o portal do Azure (ou abra novamente a [portal do Azure](https://portal.azure.com?azure-portal=true) em uma nova guia do navegador) e exiba o conteúdo do grupo de recursos em que você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.
