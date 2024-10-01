---
lab:
  title: Avaliar o desempenho do copiloto personalizado no Estúdio de IA do Azure
---

# Avaliar o desempenho do copiloto personalizado no Estúdio de IA do Azure

Neste exercício, você explorará avaliações internas e personalizadas para avaliar e comparar o desempenho dos seus aplicativos de IA com o Estúdio de IA do Azure.

Este exercício levará aproximadamente **30** minutos.

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
        - Leste da Austrália
        - Leste do Canadá
        - Leste dos EUA
        - Leste dos EUA 2
        - França Central
        - Leste do Japão
        - Centro-Norte dos EUA
        - Suécia Central
        - Norte da Suíça
        - Sul do Reino Unido
    - **Conecte os Serviços de IA do Azure ou do OpenAI do Azure**: *Crie uma nova conexão*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente. Saiba mais sobre a [disponibilidade do modelo por região](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Revise a configuração e crie o projeto.
1. Aguarde até que seu projeto seja criado.

## Implantar um modelo GPT

Para usar um modelo de linguagem no prompt flow, você precisa primeiro implantar um modelo. O Estúdio de IA do Azure permite implantar modelos do OpenAI que você pode usar nos seus fluxos.

1. No painel de navegação à esquerda, em **Componentes**, selecione a página **Implantações**.
1. Crie uma implantação do modelo **gpt-35-turbo** com as seguintes configurações:
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Versão do modelo**: *Selecione a versão padrão*
    - **Tipo de implantação**: Padrão
    - **Recurso conectado do OpenAI do Azure**: *Selecione a conexão padrão*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: Padrão
1. Aguarde até que o modelo seja implantado. Quando a implantação estiver pronta, selecione **Abrir no playground**.
1. Altere a **Mensagem do sistema** para o seguinte:

   ```
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Selecione **Aplicar alterações**.
1. Na janela de chat, insira a consulta: `What can you do?` para verificar se o modelo de idioma está se comportando conforme o esperado.

Agora que você tem um modelo implantado com uma mensagem de sistema atualizada, você pode avaliar o modelo.

## Avaliar manualmente um modelo de linguagem no Estúdio de IA do Azure

Você pode revisar manualmente as respostas do modelo com base nos dados do teste. A revisão manual permite que você teste diferentes entradas, uma de cada vez, para avaliar se o modelo funciona conforme o esperado.

1. No **playground Chat**, clique no menu suspenso **Avaliar** na barra superior e selecione **Avaliação manual**.
1. Altere a **Mensagem do sistema** para a mesma mensagem usada acima (incluída aqui novamente):

   ```
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Na seção **Resultados de avaliação manual**, você adicionará cinco entradas para as quais examinará a saída. Insira as cinco perguntas a seguir como cinco **Entradas** separadas:

   `Can you provide a list of the top-rated budget hotels in Rome?`

   `I'm looking for a vegan-friendly restaurant in New York City. Can you help?`

   `Can you suggest a 7-day itinerary for a family vacation in Orlando, Florida?`

   `Can you help me plan a surprise honeymoon trip to the Maldives?`

   `Are there any guided tours available for the Great Wall of China?`

1. Selecione **Executar** na barra superior para gerar saídas para todas as perguntas que você adicionou como entradas.
1. Agora você pode examinar manualmente as saídas de cada pergunta selecionando o ícone de polegares para cima ou para baixo na parte inferior direita de uma resposta. Classifique cada resposta, garantindo que você inclua pelo menos um polegar para cima e um polegar para baixo na sua classificação.
1. Selecione **Salvar resultados** na barra superior. Insira `manual_evaluation_results` como o nome dos resultados.
1. Usando o menu à esquerda, navegue até **Avaliações**.
1. Selecione a guia **Avaliações manuais** para localizar as avaliações manuais que você acabou de salvar. Observe que você pode explorar suas avaliações manuais criadas anteriormente, continuar de onde saiu e salvar as avaliações atualizadas.

## Avaliar seu copiloto com métricas integradas

Depois de criar um copiloto com um fluxo de chat, você pode avaliar o fluxo fazendo uma execução em lote e avaliando o desempenho do fluxo com métricas integradas.

1. Clique na guia **Avaliações automatizadas** e crie uma **Nova avaliação** com as seguintes configurações: <details>  
      <summary><b>Dica de solução de problemas</b>: erro de permissões</summary>
        <p>Se você receber um erro de permissões ao criar um novo prompt flow, tente o seguinte para solucionar o problema:</p>
        <ul>
          <li>No portal do Azure, selecione o recurso Serviços de IA.</li>
          <li>Na página do IAM, na guia Identidade, confirme se é uma identidade gerenciada atribuída pelo sistema.</li>
          <li>Navegue até a conta de armazenamento associada. Na página do IAM, adicione a atribuição de função <em>Leitor de Dados do Blob de Armazenamento</em>.</li>
          <li>Em <strong>Atribuir acesso a</strong>, escolha <strong>Identidade Gerenciada</strong>, <strong>+ Selecionar membros</strong> e selecione <strong>Todas as identidades gerenciadas atribuídas pelo sistema</strong>.</li>
          <li>Revise e atribua para salvar as novas configurações e repita a etapa anterior.</li>
        </ul>
    </details>

    - **O que você quer avaliar?**: conjunto de dados
    - **Nome da avaliação**: *Insira um nome exclusivo*
    - **Que tipo de cenário você está avaliando?**: Pergunta e resposta sem contexto
    - **Selecione os dados que você quer avaliar**: Adiciona seu conjunto de dados
        - Baixe o arquivo JSONL https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl e carregue-o na interface do usuário.
    - **Selecionar métricas**: Coerência, Fluência
    - **Conexão**: *Sua conexão dos Serviços de IA*
    - **Nome/Modelo da implantação**: *Seu modelo GPT-3.5 implantado*
1. Aguarde até que as avaliações sejam concluídas, talvez você precise atualizar.
1. Selecione a execução de avaliação que você acabou de criar.
1. Explore o **Painel de métricas** e **Resultados detalhados das métricas**.

## Excluir recursos do Azure

Quando terminar de explorar o Estúdio de IA do Azure, exclua os recursos criados para evitar custos desnecessários do Azure.

- Navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`.
- No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
- Selecione o grupo de recursos criado para este exercício.
- Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
- Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
