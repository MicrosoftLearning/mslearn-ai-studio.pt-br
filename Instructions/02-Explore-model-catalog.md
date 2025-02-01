---
lab:
  title: 'Explorar, implantar e conversar com modelos de linguagem no Azure IA Foundry'
---

# Explorar, implantar e conversar com modelos de linguagem no Azure IA Foundry

O catálogo de modelos do Azure IA Foundry serve como um repositório central onde você pode explorar e usar uma variedade de modelos, facilitando a criação do cenário de IA generativa.

Neste exercício, você explorará o catálogo de modelos no portal do Azure IA Foundry.

Este exercício levará aproximadamente **25** minutos.

## Criar um projeto e hub de IA do Azure

Um hub IA do Azure fornece um espaço de trabalho colaborativo no qual você pode definir um ou mais *projetos*. Vamos criar um projeto e hub de IA do Azure.

1. Em um navegador da Web, abra o [portal do Azure IA Foundry](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure.

1. Na home page, selecione **+Criar projeto**. No assistente **Criar um projeto**, você pode ver todos os recursos do Azure que serão criados automaticamente com seu projeto ou pode personalizar as seguintes configurações selecionando **Personalizar** antes de selecionar **Criar**:

    - **Nome do hub**: *Um nome exclusivo*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *Um novo grupo de recursos*
    - **Local**: Selecione **Ajude-me a escolher** e, em seguida, selecione **gpt-35-turbo** na janela Auxiliar de local e use a região recomendada\*
    - **Conectar os Serviços de IA do Azure ou a OpenAI do Azure**: (novo) *Preenchimentos automáticos com o nome do hub selecionado*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas no auxiliar de localização incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente. Saiba mais sobre a [disponibilidade do modelo por região](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Se você selecionou **Personalizar**, selecione **Avançar** e revise sua configuração.
1. Clique em **Criar** e aguarde a conclusão do processo.
   
    Após a criação do projeto e hub de IA do Azure, você deverá ver algo semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um hub de IA do Azure no portal do Azure AI Foundry.](./media/azure-ai-resource.png)

1. Abra uma nova guia do navegador (deixando a guia do Azure AI Foundry aberta) e navegue até o portal do Azure em [https://portal.azure.com](https://portal.azure.com?azure-portal=true), entrando com suas credenciais do Azure, se solicitado.
1. Navegue até ao grupo de recursos onde criou o seu hub IA do Azure e veja os recursos Azure que foram criados.

    ![Captura de tela de um hub de IA do Azure e recursos relacionados no portal do Azure.](./media/azure-portal.png)

1. Retorne à guia do navegador do portal do Azure AI Foundry.
1. Veja cada uma das páginas no painel do lado esquerdo da página do seu hub IA do Azure e observe os artefatos que pode criar e gerir. Na página **Centro de gerenciamento**, você pode selecionar **Recursos conectados**, no hub ou no projeto, e observar que as conexões com o OpenAI do Azure e os serviços de IA já foram criadas.
1. Se você estiver na página Centro de gerenciamento, selecione **Ir para o projeto**.

## Escolher um modelo usando parâmetros de comparação de modelo

Antes de implantar um modelo, você pode explorar os parâmetros de comparação de modelo para decidir qual modelo melhor atende às suas necessidades.

Imagine que você quer criar um copiloto personalizado que serve como assistente de viagem. Especificamente, você quer que seu copiloto ofereça suporte para consultas relacionadas a viagens, como requisitos de visto, previsões do tempo, atrações locais e normas culturais.

Seu copiloto precisará fornecer informações factualmente precisas, portanto, o embasamento é importante. Além disso, você quer que as respostas do copiloto sejam fáceis de ler e entender. Portanto, você também deve escolher um modelo com alto índice de fluência e coerência.

1. No portal do projeto da Fábrica de IA do Azure, navegue até **Catálogo de modelos** usando o menu à esquerda.
    Na página do catálogo, selecione **Comparar com parâmetros de comparação**. Na página Modelo de parâmetro de comparação, você encontrará um gráfico já plotado para você, comparando diferentes modelos.
1. Selecione **+ Modelo para comparar** e adicione **gpt-4-32k** e **gpt-4** ao gráfico de métricas. No menu suspenso **Eixo X**, em **Qualidade**, selecione as seguintes métricas e observe cada gráfico resultante antes de passar para o próximo:
    - Coerência
    - Fluência
    - Fundamentação
1. Ao explorar os resultados, você pode tentar responder às seguintes perguntas:
    - Você percebe uma diferença de desempenho entre os modelos GPT-3.5 e GPT-4?
    - Existe alguma diferença entre versões do mesmo modelo?
    - Como a variante de 32k do GPT-4 difere do modelo básico?

Na coleção OpenAI do Azure, você pode escolher entre os modelos GPT-3.5 e GPT-4. Vamos implantar esses dois modelos e explorar como eles se comparam para seu caso de uso.

## Implantar modelos OpenAI do Azure

Agora que explorou suas opções usando parâmetros de comparação de modelo, você já pode implantar modelos de linguagem. Você pode navegar pelo catálogo de modelos e implantar a partir daí ou pode implantar um modelo na página **Implantações**. Vamos explorar as duas opções.

### Implantar um modelo do Catálogo de modelos

Vamos começar implantando um modelo do catálogo de modelos. Você pode preferir essa opção quando quiser filtrar todos os modelos disponíveis.

1. Navegue até a página **Catálogo de modelos**, usando o menu à esquerda.
1. Pesquise e implante o modelo `gpt-35-turbo`, selecionado pela IA do Azure, com as seguintes configurações ao selecionar **Personalizar** nos detalhes de implantação:
   
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Tipo de implantação**: Padrão
    - **Versão do modelo**: *selecione a versão padrão*
    - **Recurso de IA**: *escolha o recurso criado anteriormente*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: DefaultV2
    - **Habilitar cota dinâmica**: Desabilitado

    > **Observação**: se o local atual do recurso de IA não tiver cota disponível para o modelo que você deseja implantar, será solicitado a escolher um local diferente onde um novo recurso de IA será criado e conectado ao seu projeto.

### Implantar um modelo por meio de Modelos + pontos de extremidade

Se você já sabe exatamente qual modelo quer implantar, talvez prefira fazê-lo por meio do **Modelos + pontos de extremidade**.

1. Navegue até a página **Modelos + pontos de extremidade** na seção **Meus ativos** usando o menu à esquerda.
1. Na guia **Implantações de modelo**, implante um novo modelo base com as seguintes configurações selecionando **Personalizar** nos detalhes da implantação:
    - **Modelo**: gpt-4
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Tipo de implantação**: Padrão
    - **Versão do modelo**: *selecione a versão padrão*
    - **Recurso de IA**: *escolha o recurso criado anteriormente*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: DefaultV2
    - **Habilitar cota dinâmica**: Desabilitado

## Testar modelos no playground de chat

Agora que temos dois modelos para comparar, vamos ver como os modelos se comportam em uma interação de conversa.

1. Navegue até a página **Playgrounds**, usando o menu à esquerda.
1. No **Playground de chat**, selecione sua implantação GPT-3.5.
1. Na janela de chat, insira a consulta `What can you do?` e veja a resposta:
    As respostas são muito genéricas. Lembre-se de que queremos criar um copiloto personalizado que sirva como assistente de viagem. Você pode especificar o tipo de ajuda que quer na pergunta que fizer.
1. Na janela de chat, insira a consulta `Imagine you're a travel assistant, what can you help me with?` As respostas já são mais específicas. Você pode não querer que seus usuários finais tenham que fornecer o contexto necessário toda vez que interagirem com seu copiloto. Para adicionar instruções globais, você pode editar a mensagem do sistema.
1. Em **Configuração**, atualize o campo **Dê instruções e contexto ao modelo** com o seguinte prompt:

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Selecione **Aplicar alterações**.
1. Na janela de chat, insira a consulta `What can you do?` e exiba a nova resposta. Observe como é diferente da resposta que você recebeu antes. A resposta é específica para viagem agora.
1. Continue a conversa perguntando: `I'm planning a trip to London, what can I do there?` O copiloto oferece muitas informações relacionadas à viagem. Você pode querer melhorar a saída ainda. Por exemplo, você pode querer que a resposta seja mais sucinta.
1. Atualize a mensagem do sistema adicionando `Answer with a maximum of two sentences.` ao final da mensagem. Aplique a alteração, limpe o chat e teste-o novamente perguntando: `I'm planning a trip to London, what can I do there?` Você também pode querer que seu copiloto continue a conversa em vez de simplesmente responder à pergunta.
1. Atualize o contexto do modelo adicionando `End your answer with a follow-up question.` ao final do prompt. Salve a alteração e teste o chat novamente perguntando: `I'm planning a trip to London, what can I do there?`
1. Altere a **implantação** para seu modelo GPT-4 e repita todas as etapas desta seção. Observe como os modelos podem ter saídas diferentes.
1. Por fim, teste os dois modelos na consulta `Who is the prime minister of the UK?`. O desempenho nesta questão está relacionado à fundamentação (se a resposta é factualmente precisa) dos modelos. O desempenho se correlaciona com suas conclusões dos parâmetros de comparação dos modelos?

Agora que você explorou os dois modelos, considere qual modelo você escolheria agora para seu caso de uso. A princípio, as saídas dos modelos podem ser diferentes e você pode preferir um modelo ao outro. No entanto, depois de atualizar a mensagem do sistema, você pode notar que a diferença é mínima. Do ponto de vista da otimização de custos, você pode optar pelo modelo GPT-3.5 em vez do modelo GPT-4, pois o desempenho dos dois é muito semelhante.

## Limpar

Se tiver terminado de explorar o portal do Azure AI Foundry, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Retorne à guia do navegador que contém o portal do Azure (ou abra novamente a [portal do Azure](https://portal.azure.com?azure-portal=true) em uma nova guia do navegador) e exiba o conteúdo do grupo de recursos em que você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.
