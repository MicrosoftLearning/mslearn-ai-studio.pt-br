---
lab:
  title: Ajustar um modelo de linguagem
  description: Saiba como usar seus próprios dados de treinamento para ajustar um modelo e personalizar seu comportamento.
---

# Ajustar um modelo de linguagem

Quando você deseja que um modelo de linguagem se comporte de uma determinada maneira, pode usar a engenharia de prompt para definir o comportamento desejado. Quando você deseja melhorar a consistência do comportamento desejado, pode optar por ajustar um modelo, comparando-o com sua abordagem de engenharia de prompt para avaliar qual método melhor atende às suas necessidades.

Neste exercício, você ajustará um modelo de linguagem com o Azure AI Foundry que você quer usar para um cenário de aplicativo de chat personalizado. Você comparará o modelo ajustado com um modelo base para avaliar se o modelo ajustado atende melhor às suas necessidades.

Imagine que você trabalha para uma agência de viagens e está desenvolvendo um aplicativo de chat para ajudar as pessoas a planejar as férias. O objetivo é criar um bate-papo simples e inspirador que sugira destinos e atividades com um tom de conversa consistente e amigável.

Este exercício levará aproximadamente **60** minutos\*.

> \***Observação**: esse tempo é uma estimativa baseada na experiência média. O ajuste depende dos recursos de infraestrutura em nuvem, que podem levar um tempo variável para provisionar, dependendo da capacidade do data center e da demanda simultânea. Algumas atividades neste exercício podem levar <u>muito</u> tempo para serem concluídas e demandam paciência. Se as coisas estiverem demorando um pouco, considere revisar a [Documentação de ajuste fino da Fábrica de IA do Azure](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) ou fazer uma pausa.

## Criar um projeto e hub de IA no portal do Azure AI Foundry

Vamos começar criando um projeto do Portal da Fábrica de IA do Azure dentro de um hub da IA do Azure:

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir:

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. Na home page, selecione **+Criar projeto**.
1. No assistente **Criar um projeto**, digite um nome válido para o seu projeto e, se um hub existente for sugerido, escolha a opção para criar um novo. Em seguida, examine os recursos do Azure que serão criados automaticamente para dar suporte ao hub e ao projeto.
1. Selecione **Personalizar** e especifique as seguintes configurações para o hub:
    - **Nome do hub**: *um nome válido para o seu hub*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Localização**: selecione **Ajude-me a escolher** e então selecione **gpt-4-finetune** na janela do Auxiliar de localização e use a região recomendada\*
    - **Conectar os Serviços de IA do Azure ou o OpenAI do Azure**: *Criar um novo recurso de Serviços de IA*
    - **Conectar a Pesquisa de IA do Azure**: *crie um novo recurso da Pesquisa de IA do Azure com um nome exclusivo*

    > \* Os recursos do OpenAI do Azure são restritos por cotas regionais. Caso um limite de cota seja excedido posteriormente no exercício, é possível que você precise criar outro recurso em uma região diferente. 

1. Clique em **Avançar** e revise a configuração. Em seguida, selecione **Criar** e aguarde a conclusão do processo.
1. Quando o projeto for criado, feche todas as dicas exibidas e examine a página do projeto no Portal da Fábrica de IA do Azure, que deve ser semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um projeto IA do Azure no Portal da Fábrica de IA do Azure.](./media/ai-foundry-project.png)

## Ajuste um modelo

Como o ajuste fino de um modelo leva algum tempo para ser concluído, você iniciará o trabalho de ajuste fino agora e voltará a ele depois de explorar um modelo básico que não tenha sido ajustado para fins de comparação.

1. Baixe o [conjunto de dados de treinamento](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) em `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl`e salve-o como um arquivo JSONL localmente.

    > **Observação**: seu dispositivo pode salvar o arquivo como um arquivo .txt por padrão. Selecione todos os arquivos e remova o sufixo .txt para garantir que você esteja salvando o arquivo como JSONL.

1. Navegue até a página **Ajustar** na seção **Criar e personalizar**, usando o menu à esquerda.
1. Selecione o botão para adicionar um novo modelo de ajuste fino, selecione o modelo **gpt-4o** e, em seguida, selecione **Avançar**.
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
1. O ajuste será iniciado e pode levar algum tempo para ser concluído. Você pode continuar com a próxima seção do exercício enquanto espera.

> **Observação**: o ajuste e a implantação podem levar um tempo considerável (30 minutos ou mais), portanto, talvez seja necessário verificar periodicamente. Você pode ver mais detalhes do progresso até agora selecionando o trabalho de modelo de ajuste fino e exibindo sua guia **Logs** .

## Chat com um modelo básico

Enquanto você espera a conclusão do trabalho de ajuste, vamos conversar com um modelo GPT-4o básico para avaliar o desempenho.

1. No painel à esquerda do seu projeto, na seção **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Na página **Modelos + pontos extremidades**, na guia **Implantações de modelo**, no menu **+ Implantar modelo**, selecione **Implantar modelo base**.
1. Procure o modelo **gpt-4** na lista, selecione-o e confirme-o.
1. Crie uma nova implantação do modelo com as seguintes configurações selecionando **Personalizar** nos detalhes de implantação:
    - **Nome da implantação**: *Um nome válido para sua implantação de modelo*
    - **Tipo de implantação**: padrão global
    - **Atualização automática de versão**: Ativado
    - **Versão do modelo**: *selecione a versão mais recente disponivel*
    - **Recurso de IA conectado**: *selecione sua conexão de recurso do Azure OpenAI (se o local atual do recurso de IA não tiver cota disponível para o modelo que você deseja implantar, será solicitado que você escolha um local diferente onde um novo recurso de IA será criado e conectado ao seu projeto)*
    - **Limite de taxa de tokens por minuto (milhares):** 50 mil *(ou o máximo disponível em sua assinatura, se inferior a 50 mil)*
    - **Filtro de conteúdo**: DefaultV2

    > **Observação**: A redução do TPM ajuda a evitar o uso excessivo da cota disponível na assinatura que você está usando. 50.000 TPM são suficientes para os dados usados neste exercício. Se a sua cota disponível for menor do que isso, você poderá concluir o exercício, mas poderá ocorrer erros se o limite de taxa for excedido.

1. Aguarde até que a implantação seja concluída.

> **Observação**: se o local atual do recurso de IA não tiver cota disponível para o modelo que você deseja implantar, será solicitado a escolher um local diferente onde um novo recurso de IA será criado e conectado ao seu projeto.

1. Quando a implantação estiver finalizada, selecione o botão **Abrir no playground**.
1. Verifique se o modelo básico do gpt-4o implantado está selecionado no painel de configuração.
1. Na janela de chat, insira a consulta `What can you do?` e veja a resposta:

    As respostas podem ser bastante genéricas. Lembre-se de que queremos criar um aplicativo de chat que inspire as pessoas a viajar.

1. Atualize a mensagem do sistema no painel de configuração com o seguinte prompt:

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Selecione **Aplicar alterações**, selecione **Limpar chat** e pergunte novamente `What can you do?`. Como resposta, o assistente pode informar que pode ajudar você a reservar voos, hotéis e alugar carros para sua viagem. Você quer evitar esse comportamento.
1. Atualize a mensagem do sistema novamente com um novo prompt:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Selecione **Aplicar alterações** e **Limpar chat**.
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

    > **Dica**: Use o botão **Atualizar** na página de ajuste fino para atualizar a visualização. Se o trabalho de ajuste fino desaparecer completamente, atualize a página no navegador.

1. Selecione o link do trabalho de ajuste fino para abrir sua página de detalhes. Selecione a guia **Métricas** e explore as métricas de ajuste.
1. Implante o modelo ajustado com as seguintes configurações:
    - **Nome da implantação**: *Um nome válido para sua implantação de modelo*
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
