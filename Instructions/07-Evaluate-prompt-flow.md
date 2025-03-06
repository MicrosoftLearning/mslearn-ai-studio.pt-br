---
lab:
  title: Avalie o desempenho da IA generativa
  description: Aprenda a avaliar modelos e fluxos de chat para otimizar o desempenho do seu aplicativo de chat e sua capacidade de responder adequadamente.
---

# Avalie o desempenho da IA generativa

Neste exercício, você explorará avaliações internas e personalizadas para avaliar e comparar o desempenho dos seus aplicativos de IA com o portal do Azure IA Foundry.

Este exercício levará aproximadamente **30** minutos.

## Crie um hub de IA e um projeto no Azure IA Foundry

Você deve começar criando um projeto do Azure IA Foundry em um hub de IA do Azure:

1. Em um navegador da Web, abra [https://ai.azure.com](https://ai.azure.com) e entre usando suas credenciais do Azure.
1. Selecione a **Página Inicial** e selecione **+ Novo projeto**.
1. No assistente **Criar um projeto**, dê um nome exclusivo ao seu projeto, selecione **Personalizar** e defina as seguintes configurações:
    - **Nome do hub**: *Um nome exclusivo*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *Um novo grupo de recursos*
    - **Localização**: selecione **Ajude-me a escolher** e então selecione **gpt-4** na janela do auxiliar de localização e use a região recomendada\*
    - **Conectar os Serviços de IA do Azure ou a OpenAI do Azure**: (novo) *preenchido automaticamente com o nome do hub selecionado*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas no auxiliar de localização incluem a cota padrão para os tipos de modelos usados neste exercício. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente. Saiba mais sobre a [disponibilidade do modelo por região](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#availability)

1. Clique em **Avançar** e revise a configuração.
1. Clique em **Criar um projeto** e aguarde a conclusão do processo.

## Implantar um modelo GPT

Para usar um modelo de linguagem no prompt flow, você precisa primeiro implantar um modelo. O portal do Azure AI Foundry permite implantar modelos do OpenAI que você pode usar nos seus fluxos.

1. Navegue até a página **Modelos + pontos de extremidade** na seção **Meus ativos** usando o menu à esquerda.
1. Selecione o botão **+ Implantar modelo** e selecione a opção **Implantar modelo base**.
1. Crie uma nova implantação do modelo **gpt-4** com as seguintes configurações selecionando **Personalizar** no assistente **Implantar modelo**:
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Tipo de implantação**: Padrão
    - **Versão do modelo**: *selecione a versão padrão*
    - **Recurso de IA**: *escolha o recurso criado anteriormente*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: DefaultV2
    - **Habilitar cota dinâmica**: Desabilitado

    > **Observação**: se o local atual do recurso de IA não tiver cota disponível para o modelo que você deseja implantar, será solicitado a escolher um local diferente onde um novo recurso de IA será criado e conectado ao seu projeto.

1. Aguarde até que o modelo seja implantado. Quando a implantação estiver pronta, selecione **Abrir no playground**.
1. Na caixa de texto **Fornecer instruções e contexto ao modelo**, altere o conteúdo para o seguinte:

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
1. Na janela de chat (histórico), insira a consulta: `What can you do?` para verificar se o modelo de linguagem está se comportando conforme o esperado.

Agora que você tem um modelo implantado com uma mensagem de sistema atualizada, você pode avaliar o modelo.

## Avaliar manualmente um modelo de linguagem no portal do Azure IA Foundry

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
1. Usando o menu à esquerda, navegue até **Avaliação**.
1. Selecione a guia **Avaliações manuais** para localizar as avaliações manuais que você acabou de salvar. Observe que você pode explorar suas avaliações manuais criadas anteriormente, continuar de onde parou e salvar as avaliações atualizadas.

## Avalie seu aplicativo de chat com as métricas integradas

Depois de criar um aplicativo de chat com o prompt flow, é possível avaliar o fluxo fazendo uma execução em lote e avaliando o desempenho do fluxo com métricas integradas.

![Diagrama de construção do conjunto de dados de entrada para avaliação.](./media/diagram-dataset-evaluation.png)

Para avaliar um fluxo de chat, as consultas do usuário e as respostas do chat são fornecidas como entrada para uma avaliação.

Para economizar tempo, criamos um conjunto de dados de saída em lote para você que contém os resultados de várias entradas sendo processadas por um prompt flow. Cada um dos resultados é armazenado no conjunto de dados que você avaliará na próxima etapa.

1. Clique na guia **Avaliações automatizadas** e crie uma **Nova avaliação** com as seguintes configurações: <details>  
      <summary><b>Dica de solução de problemas</b>: erro de permissões</summary>
        <p>Se você receber um erro de permissões ao criar um novo prompt flow, tente o seguinte para solucionar o problema:</p>
        <ul>
          <li>No portal do Azure, selecione o recurso Serviços de IA.</li>
          <li>Na guia Identidade, em Gerenciamento de recursos, confirme se é uma identidade gerenciada atribuída pelo sistema.</li>
          <li>Navegue até a conta de armazenamento associada. Na página do IAM, adicione a atribuição de função <em>Leitor de Dados do Blob de Armazenamento</em>.</li>
          <li>Em <strong>Atribuir acesso a</strong>, escolha <strong>Identidade Gerenciada</strong>, <strong>+ Selecionar membros</strong> e selecione <strong>Todas as identidades gerenciadas atribuídas pelo sistema</strong>.</li>
          <li>Revise e atribua para salvar as novas configurações e repita a etapa anterior.</li>
        </ul>
    </details>

    - **O que você quer avaliar?**: conjunto de dados
    - **Nome da avaliação**: *Insira um nome exclusivo*
    - Selecione **Avançar**
    - **Selecione os dados que você quer avaliar**: Adiciona seu conjunto de dados
        - Faça o download do [conjunto de dados de validação](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl) em `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl`, salve-o como um arquivo JSONL e carregue-o na interface do usuário.

    > **Observação**: seu dispositivo pode salvar o arquivo como um arquivo .txt por padrão. Selecione todos os arquivos e remova o sufixo .txt para garantir que você esteja salvando o arquivo como JSONL.

    - Selecione **Avançar**
    - **Selecionar métricas**: Coerência, Fluência
    - **Conexão**: *Sua conexão dos Serviços de IA*
    - **Nome/Modelo da implantação**: *Seu modelo GPT-4 implantado*
    - **consulta**: selecione **consulta** como a fonte de dados
    - **resposta**: selecione **resposta** como a fonte de dados
      
1. Selecione **Avançar**, revise os dados e **Envie** a nova avaliação.
1. Aguarde até que as avaliações sejam concluídas, talvez você precise atualizar.
1. Selecione a execução de avaliação que você acabou de criar.
1. Explore o **Painel de métricas** e **Resultados detalhados das métricas**.

## Excluir recursos do Azure

Quando terminar de explorar o Azure IA Foundry, exclua os recursos criados para evitar custos desnecessários do Azure.

- Navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`.
- No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
- Selecione o grupo de recursos criado para este exercício.
- Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
- Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
