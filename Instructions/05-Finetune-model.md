---
lab:
  title: Ajustar um modelo de linguagem
  description: Saiba como usar seus próprios dados de treinamento para ajustar um modelo e personalizar seu comportamento.
---

# Ajustar um modelo de linguagem

Quando você deseja que um modelo de linguagem se comporte de uma determinada maneira, pode usar a engenharia de prompt para definir o comportamento desejado. Quando você deseja melhorar a consistência do comportamento desejado, pode optar por ajustar um modelo, comparando-o com sua abordagem de engenharia de prompt para avaliar qual método melhor atende às suas necessidades.

Neste exercício, você ajustará um modelo de linguagem com o Azure AI Foundry que você quer usar para um cenário de aplicativo de chat personalizado. Você comparará o modelo ajustado com um modelo base para avaliar se o modelo ajustado atende melhor às suas necessidades.

Imagine que você trabalha para uma agência de viagens e está desenvolvendo um aplicativo de chat para ajudar as pessoas a planejar as férias. O objetivo é criar um chat simples e inspirador que sugira destinos e atividades com um tom de conversa consistente e amigável.

Este exercício levará aproximadamente **60** minutos\*.

> \***Observação**: esse tempo é uma estimativa baseada na experiência média. O ajuste depende dos recursos de infraestrutura em nuvem, que podem levar um tempo variável para provisionar, dependendo da capacidade do data center e da demanda simultânea. Algumas atividades neste exercício podem levar <u>muito</u> tempo para serem concluídas e demandam paciência. Se as coisas estiverem demorando um pouco, considere revisar a [Documentação de ajuste fino da Fábrica de IA do Azure](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) ou fazer uma pausa. É possível que alguns processos atinjam o tempo limite ou pareçam ser executados indefinidamente. Algumas das tecnologias usadas neste exercício estão em versão prévia ou em desenvolvimento ativo. Você pode experimentar algum comportamento inesperado, avisos ou erros.

## Implantar um modelo em um projeto da Fábrica de IA do Azure

Vamos começar implantando um modelo em um projeto da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir (feche o painel **Ajuda** se estiver aberto):

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. Na home page, na seção **Explorar modelos e recursos**, pesquise pelo modelo `gpt-4o`, que usaremos em nosso projeto.
1. Nos resultados da pesquisa, selecione o modelo **gpt-4o** para ver os detalhes e, na parte superior da página do modelo, clique em **Usar este modelo**.
1. Quando solicitado a criar um projeto, insira um nome válido para o projeto e expanda **Opções avançadas**.
1. Selecione **Personalizar** e especifique as seguintes configurações para o seu projeto:
    - **Recurso da Fábrica de IA do Azure**: *um nome válido para o recurso da Fábrica de IA do Azure*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Região**: *selecione uma das seguintes regiões*:\*
        - Leste dos EUA 2
        - Centro-Norte dos EUA
        - Suécia Central

    > \* No momento da redação deste documento, essas regiões oferecem suporte ao ajuste fino dos modelos gpt-4o.

1. Clique em **Criar** e aguarde a criação do projeto, incluindo a implantação do modelo gpt-4 selecionado.
1. Quando o projeto for criado, o playground chat abrirá automaticamente.
1. No painel **Configuração**, anote o nome da implantação do modelo; que será **gpt-4o**. Você pode confirmar isso visualizando a implantação na página **Modelos e pontos de extremidade** (basta abrir essa página no painel de navegação à esquerda).
1. No painel de navegação à esquerda, selecione **Visão geral** para ver a página principal do projeto, que será assim:

    ![Captura de tela de uma página de visão geral do projeto da Fábrica de IA do Azure.](./media/ai-foundry-project.png)

## Ajuste um modelo

Como o ajuste fino de um modelo leva algum tempo para ser concluído, você iniciará o trabalho de ajuste agora e o retomará depois de explorar um modelo gpt-4o básico que você já implantou.

1. Baixe o [conjunto de dados de treinamento](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) em `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl`e salve-o como um arquivo JSONL localmente.

    > **Observação**: seu dispositivo pode salvar o arquivo como um arquivo .txt por padrão. Selecione todos os arquivos e remova o sufixo .txt para garantir que você esteja salvando o arquivo como JSONL.

1. Navegue até a página **Ajustar** na seção **Criar e personalizar**, usando o menu à esquerda.
1. Selecione o botão para adicionar um novo modelo de ajuste fino, selecione o modelo **gpt-4o** e, em seguida, selecione **Avançar**.
1. **Ajuste** o modelo usando a seguinte configuração:
    - **Método de personalização**: Supervisionado
    - **Modelo básico**: *selecione a versão padrão do **gpt-4o***
    - **Dados de treinamento**: *clique na opção **Adicionar dados de treinamento** e, em seguida, carregue e aplique o arquivo .jsonl que você baixou anteriormente*
    - **Sufixo do modelo**: `ft-travel`
    - **Semente**: *Aleatória
1. Envie os detalhes do ajuste fino e o trabalho será iniciado. Pode levar algum tempo para isso ser concluído. Você pode continuar com a próxima seção do exercício enquanto espera.

> **Observação**: o ajuste e a implantação podem levar um tempo considerável (30 minutos ou mais), portanto, talvez seja necessário verificar periodicamente. Você pode ver mais detalhes do progresso até agora selecionando o trabalho de modelo de ajuste fino e exibindo a guia **Logs**.

## Chat com um modelo básico

Enquanto você espera a conclusão do trabalho de ajuste, vamos conversar com um modelo GPT-4o básico para avaliar o desempenho.

1. No painel de navegação à esquerda, selecione **Playgrounds** e abra o **Playground de chat**.
1. Verifique se o modelo básico **gpt-4o** implantado está selecionado no painel de configuração.
1. Na janela de chat, insira a consulta `What can you do?` e veja a resposta:

    As respostas podem ser bastante genéricas. Lembre-se de que queremos criar um aplicativo de chat que inspire as pessoas a viajar.

1. Atualize a mensagem do sistema no painel de configuração com o seguinte prompt:

    ```
    You are an AI assistant that helps people plan their travel.
    ```

1. Clique em **Aplicar alterações** para atualizar a mensagem do sistema.
1. Na janela de chat, insira a consulta `What can you do?` novamente e veja a resposta.
1 Como resposta, o assistente pode informar que pode ajudar você a reservar voos, hotéis e alugar carros para sua viagem. Você quer evitar esse comportamento.

1. Atualize a mensagem do sistema novamente com um novo prompt:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```
.
1. Continue testando seu aplicativo de chat para verificar se ele não fornece nenhuma informação que não esteja fundamentada nos dados recuperados. Por exemplo, faça as seguintes perguntas e revise as respostas do modelo, prestando atenção especial ao tom e ao estilo de escrita que o modelo usa para responder:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

## Revise o arquivo de treinamento

O modelo base parece funcionar bem o suficiente, mas você pode estar procurando um estilo de conversação específico em seu aplicativo de IA generativa. Os dados de treinamento usados para ajuste fino oferecem a você a chance de criar exemplos explícitos dos tipos de resposta que você deseja.

1. Abra o arquivo JSONL que você baixou anteriormente (você pode abri-lo em qualquer editor de texto)
1. Examine a lista dos documentos JSON no arquivo de dados de treinamento. O primeiro deve ser semelhante a este (formatado para facilitar a leitura):

    ```json
    {"messages": [
        {"role": "system", "content": "You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms. You should not provide any hotel, flight, rental car or restaurant recommendations. Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday."},
        {"role": "user", "content": "What's a must-see in Paris?"},
        {"role": "assistant", "content": "Oh la la! You simply must twirl around the Eiffel Tower and snap a chic selfie! After that, consider visiting the Louvre Museum to see the Mona Lisa and other masterpieces. What type of attractions are you most interested in?"}
        ]}
    ```

    Cada exemplo de interação na lista inclui a mesma mensagem do sistema que você testou com o modelo base, um prompt do usuário relacionado a uma consulta de viagem e uma resposta. O estilo das respostas nos dados de treinamento ajudará o modelo ajustado a aprender como ele deve responder.

## Implantar o modelo ajustado

Quando o ajuste for concluído, você poderá implantar o modelo ajustado.

1. Navegue até a página **Ajustar** em **Criar e personalizar** para encontrar seu trabalho de ajuste e o status dele. Se ele ainda estiver em execução, você poderá optar por continuar conversando com seu modelo base implantado ou fazer uma pausa. Se estiver concluído, você pode continuar.

    > **Dica**: use o botão **Atualizar** na página de ajuste fino para atualizar a visualização. Se o trabalho de ajuste fino desaparecer completamente, atualize a página no navegador.

1. Selecione o link do trabalho de ajuste fino para abrir sua página de detalhes. Depois, selecione a guia **Métricas** e explore as métricas de ajuste.
1. Implante o modelo ajustado com as seguintes configurações:
    - **Nome da implantação**: *Um nome válido para a implantação de modelo*
    - **Tipo de implantação**: Padrão
    - **Limite de taxa de tokens por minuto (milhares):** 50 mil *(ou o máximo disponível em sua assinatura, se inferior a 50 mil)*
    - **Filtro de conteúdo**: Padrão
1. Aguarde a conclusão da implantação antes de testá-la, isso pode demorar um pouco. Verifique o **estado de provisionamento** até que ele seja bem-sucedido (talvez seja necessário atualizar o navegador para ver o status atualizado).

## Testar o modelo ajustado

Agora que você implantou seu modelo ajustado, pode testar o modelo da mesma forma que testou o modelo base implantado.

1. Quando a implantação estiver pronta, navegue até o modelo ajustado e selecione **Abrir no playground**.
1. Certifique-se de que a mensagem do sistema inclua estas instruções:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Teste seu modelo ajustado para avaliar se o comportamento dele é mais consistente agora. Por exemplo, faça as seguintes perguntas novamente e explore as respostas do modelo:

    `Where in Rome should I stay?`

    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

1. Depois de revisar as respostas, como elas se comparam às do modelo base?

## Limpar

Se tiver terminado de explorar o Azure AI Foundry, exclua os recursos criados para evitar custos desnecessários do Azure.

- Navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`.
- No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
- Selecione o grupo de recursos criado para este exercício.
- Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
- Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
