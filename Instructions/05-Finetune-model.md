---
lab:
  title: Ajustar um modelo de linguagem para conclusão de chat no Estúdio de IA do Azure
---

# Ajustar um modelo de linguagem para conclusão de chat no Estúdio de IA do Azure

Neste exercício, você ajustará um modelo de linguagem com o Estúdio de IA do Azure AI que você quer usar para um cenário de copiloto personalizado.

Este exercício levará, aproximadamente, **45** minutos.

## Crie um hub de IA e um projeto no Estúdio de IA do Azure

Você deve começar criando um projeto do Estúdio de IA do Azure em um hub de IA do Azure:

1. Em um navegador da Web, abra [https://ai.azure.com](https://ai.azure.com) e entre usando suas credenciais do Azure.
1. Selecione a **Página Inicial** e selecione **+ Novo projeto**.
1. No assistente **Criar um projeto**, crie um projeto com as seguintes configurações:
    - **Nome do projeto**: *Um nome exclusivo para seu projeto*
    - **Hub**: *Crie um novo hub com as seguintes configurações:*
        - **Nome do hub**: *Um nome exclusivo*
        - **Assinatura**: *sua assinatura do Azure*
        - **Grupo de recursos**: *Um novo grupo de recursos*
        - **Localização**: *faça uma escolha **aleatória** de uma das regiões a seguir*\*
        - Leste dos EUA 2
        - Centro-Norte dos EUA
        - Suécia Central
        - Norte da Suíça
    - **Conecte os Serviços de IA do Azure ou do OpenAI do Azure**: *Crie uma nova conexão*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente. Saiba mais sobre a [disponibilidade do modelo por região](https://learn.microsoft.com/en-us/azure/ai-studio/concepts/fine-tuning-overview#azure-openai-models)

1. Revise a configuração e crie o projeto.
1. Aguarde até que seu projeto seja criado.

## Ajustar um modelo GPT-3.5

Antes de ajustar um modelo, você precisa de um conjunto de dados.

1. Salve o conjunto de dados de treinamento como arquivo JSONL localmente: https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl
1. Navegue até a página **Ajuste** na seção **Ferramentas**, usando o menu à esquerda.
1. Clique no botão para adicionar um novo modelo de ajuste, selecione o modelo `gpt-35-turbo` e clique em **Confirmar**.
1. **Ajuste** o modelo usando a seguinte configuração:
    - **Versão do modelo**: *Selecione a versão padrão*
    - **Sufixo do modelo**: `ft-travel`
    - **Conexão do OpenAI do Azure**: *selecione a conexão que foi criada quando você criou seu hub*
    - **Dados de treinamento**: carregar arquivos
    - **Carregar arquivo**: escolha o arquivo JSONL que você baixou na etapa anterior.

    > **Dica**: você não precisa esperar que o processamento de dados seja concluído para continuar para a próxima etapa.

    - **Dados de validação**: nenhum
    - **Parâmetros da tarefa**: *mantenha as configurações padrão*
1. O ajuste será iniciado e pode levar algum tempo para ser concluído.

> **Observação**: o ajuste e a implantação podem levar algum tempo, portanto, talvez seja necessário verificar periodicamente para concluir a próxima etapa.

## Implantar o modelo ajustado

Quando o ajuste for concluído, você poderá implantar o modelo.

1. Selecione o modelo ajustado. Selecione a guia **Métricas** e explore as métricas de ajuste.
1. Implante o modelo ajustado com as seguintes configurações:
    - **Nome da implantação**: *um nome exclusivo para seu modelo, você pode usar o padrão*
    - **Tipo de implantação**: Padrão
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: Padrão

## Testar o modelo ajustado

Agora que você implantou seu modelo ajustado, pode testar o modelo da mesma forma que pode testar qualquer outro modelo implantado.

1. Quando a implantação estiver pronta, navegue até o modelo ajustado e selecione **Abrir no playground**.
1. Na janela de chat, insira a consulta `What can you do?` Observe que, embora você não tenha especificado a mensagem do sistema para instruir seu modelo a responder a perguntas relacionadas a viagens, o modelo já entende no que deve focar.
1. Tente com outra consulta como `Where should I go on holiday for my 30th birthday?`

## Excluir recursos do Azure

Quando terminar de explorar o Estúdio de IA do Azure, exclua os recursos criados para evitar custos desnecessários do Azure.

- Navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`.
- No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
- Selecione o grupo de recursos criado para este exercício.
- Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
- Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
