---
lab:
  title: Ajustar um modelo de linguagem para conclusão de chat no Azure AI Foundry
---

# Ajustar um modelo de linguagem para conclusão de chat no Azure AI Foundry

Quando você deseja que um modelo de linguagem se comporte de uma determinada maneira, pode usar a engenharia de prompt para definir o comportamento desejado. Quando você deseja melhorar a consistência do comportamento desejado, pode optar por ajustar um modelo, comparando-o com sua abordagem de engenharia de prompt para avaliar qual método melhor atende às suas necessidades.

Neste exercício, você ajustará um modelo de linguagem com o Azure AI Foundry que você quer usar para um cenário de aplicativo de chat personalizado. Você comparará o modelo ajustado com um modelo base para avaliar se o modelo ajustado atende melhor às suas necessidades.

Imagine que você trabalha para uma agência de viagens e está desenvolvendo um aplicativo de chat para ajudar as pessoas a planejar as férias. O objetivo é criar um chat simples e inspirador que sugira destinos e atividades. Como o chat não está conectado a nenhuma fonte de dados, ele **não** deve fornecer recomendações específicas para hotéis, voos ou restaurantes a fim de garantir a confiança dos clientes.

Este exercício levará aproximadamente **60** minutos.

## Criar um projeto e hub de IA no portal do Azure AI Foundry

Você deve começar criando um projeto do portal do Azure AI Foundry em um hub de IA do Azure:

1. Em um navegador da Web, abra [https://ai.azure.com](https://ai.azure.com) e entre usando suas credenciais do Azure.
1. Na home page, selecione **+ Criar um projeto**.
1. No assistente **Criar um projeto**, crie um projeto com as seguintes configurações:
    - **Nome do projeto**: *Um nome exclusivo para seu projeto*
    - Selecione **Personalizar**.
        - **Hub**: *preenchido automaticamente com o nome padrão*
        - **Assinatura**: *preenchido automaticamente com sua conta conectada*
        - **Grupo de recursos**: (Novo) *preenchido automaticamente com o nome do seu projeto*
        - **Localização**: escolha uma das seguintes regiões **Leste dos EUA2**, **Centro-Norte dos EUA**, **Suécia Central**, **Oeste da Suíça**\*
        - **Conectar os Serviços de IA do Azure ou a OpenAI do Azure**: (novo) *preenchido automaticamente com o nome do hub selecionado*
        - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas no auxiliar de localização incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente. Saiba mais sobre [Ajuste fino de regiões de modelo](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models?tabs=python-secure%2Cglobal-standard%2Cstandard-chat-completions#fine-tuning-models)

1. Revise a configuração e crie o projeto.
1. Aguarde até que seu projeto seja criado.

## Ajustar um modelo GPT-3.5

Como o ajuste fino de um modelo leva algum tempo para ser concluído, você iniciará o trabalho de ajuste fino primeiro. Antes de ajustar um modelo, você precisa de um conjunto de dados.

1. Baixe o [conjunto de dados de treinamento](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) em `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl`e salve-o como um arquivo JSONL localmente.

    > **Observação**: seu dispositivo pode salvar o arquivo como um arquivo .txt por padrão. Selecione todos os arquivos e remova o sufixo .txt para garantir que você esteja salvando o arquivo como JSONL.

1. Navegue até a página **Ajustar** na seção **Criar e personalizar**, usando o menu à esquerda.
1. Selecione o botão para adicionar um novo modelo de ajuste, selecione o modelo `gpt-35-turbo`, clique em **Avançar** e clique em **Confirmar**.
1. **Ajuste** o modelo usando a seguinte configuração:
    - **Versão do modelo**: *selecione a versão padrão*
    - **Sufixo do modelo**: `ft-travel`
    - **Recurso de IA conectado**: *selecione a conexão que foi criada quando você criou seu hub. Já deve estar selecionado por padrão.*
    - **Dados de treinamento**: carregar arquivos

    <details>  
    <summary><b>Dica de solução de problemas</b>: erro de permissões</summary>
    <p>Se você receber um erro de permissões, tente o seguinte para solucionar o problema:</p>
    <ul>
        <li>No portal do Azure, selecione o recurso Serviços de IA.</li>
        <li>Na guia Identidade, em Gerenciamento de recursos, confirme se é uma identidade gerenciada atribuída pelo sistema.</li>
        <li>Navegue até a conta de armazenamento associada. Na página do IAM, adicione a atribuição de função <em>Proprietário de Dados do Blob de Armazenamento</em>.</li>
        <li>Em <strong>Atribuir acesso a</strong>, escolha <strong>Identidade Gerenciada</strong>, <strong>+Selecionar membros</strong>, selecione <strong>Todas as identidades gerenciadas atribuídas pelo sistema</strong> e selecione o recurso dos Serviços de IA do Azure.</li>
        <li>Revise e atribua para salvar as novas configurações e repita a etapa anterior.</li>
    </ul>
    </details>

    - **Carregar arquivo**: escolha o arquivo JSONL que você baixou na etapa anterior.
    - **Dados de validação**: nenhum
    - **Parâmetros da tarefa**: *mantenha as configurações padrão*
1. O ajuste será iniciado e pode levar algum tempo para ser concluído.

> **Observação**: o ajuste e a implantação podem levar algum tempo, portanto, talvez seja necessário verificar periodicamente. Você já pode continuar com a próxima etapa enquanto espera.

## Chat com um modelo básico

Enquanto você espera a conclusão do trabalho de ajuste, vamos conversar com um modelo GPT 3.5 básico para avaliar o desempenho.

1. Navegue até a página **Modelos + pontos de extremidade** na seção **Meus ativos** usando o menu à esquerda.
1. Selecione o botão **+ Implantar modelo** e selecione a opção **Implantar modelo base**.
1. Implante um modelo `gpt-35-turbo`, que é o mesmo tipo de modelo que você usou ao ajustar.

> **Observação**: se o local atual do recurso de IA não tiver cota disponível para o modelo que você deseja implantar, será solicitado a escolher um local diferente onde um novo recurso de IA será criado e conectado ao seu projeto.

1. Quando a implantação estiver finalizada, selecione o botão **Abrir no playground**.
1. Verifique se o modelo base implantado `gpt-35-model` está selecionado no painel de configuração.
1. Na janela de chat, insira a consulta `What can you do?` e veja a resposta:
    As respostas são muito genéricas. Lembre-se de que queremos criar um aplicativo de chat que inspire as pessoas a viajar.
1. Atualize a mensagem do sistema no painel de configuração com o seguinte prompt:

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Selecione **Aplicar alterações**, selecione **Limpar chat** e pergunte novamente `What can you do?`. Como resposta, o assistente pode informar que pode ajudar você a reservar voos, hotéis e alugar carros para sua viagem. Você quer evitar esse comportamento.
1. Atualize a mensagem do sistema novamente com um novo prompt:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Selecione **Aplicar alterações** e **Limpar chat**.
1. Continue testando seu aplicativo de chat para verificar se ele não fornece nenhuma informação que não esteja fundamentada nos dados recuperados. Por exemplo, faça as seguintes perguntas e explore as respostas do modelo:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

    O modelo pode fornecer uma lista de hotéis, mesmo quando você o instruiu a não dar recomendações de hotéis. Este é um exemplo de comportamento inconsistente. Vamos explorar se o modelo ajustado tem melhor desempenho nesses casos.

1. Navegue até a página **Ajustar** em **Criar e personalizar** para encontrar seu trabalho de ajuste e o status dele. Se ele ainda estiver em execução, você poderá optar por continuar avaliando manualmente seu modelo base implantado. Se estiver concluído, você pode continuar com a próxima seção.

## Implantar o modelo ajustado

Quando o ajuste for concluído, você poderá implantar o modelo ajustado.

1. Selecione o modelo ajustado. Selecione a guia **Métricas** e explore as métricas de ajuste.
1. Implante o modelo ajustado com as seguintes configurações:
    - **Nome da implantação**: *um nome exclusivo para seu modelo, você pode usar o padrão*
    - **Tipo de implantação**: Padrão
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: Padrão
1. Aguarde a conclusão da implantação antes de testá-la, isso pode demorar um pouco.

## Testar o modelo ajustado

Agora que você implantou seu modelo ajustado, pode testar o modelo da mesma forma que testou o modelo base implantado.

1. Quando a implantação estiver pronta, navegue até o modelo ajustado e selecione **Abrir no playground**.
1. Atualize a mensagem do sistema com as seguintes instruções:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Teste seu modelo ajustado para avaliar se o comportamento dele é mais consistente agora. Por exemplo, faça as seguintes perguntas novamente e explore as respostas do modelo:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

## Limpar

Se tiver terminado de explorar o Azure AI Foundry, exclua os recursos criados para evitar custos desnecessários do Azure.

- Navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`.
- No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
- Selecione o grupo de recursos criado para este exercício.
- Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
- Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
