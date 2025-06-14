---
lab:
  title: Avaliar o desempenho do modelo da IA generativa
  description: Aprenda a avaliar modelos e prompts para otimizar o desempenho do seu aplicativo de chat e sua capacidade de responder adequadamente.
---

# Avaliar o desempenho do modelo da IA generativa

Neste exercício, você usará avaliações manuais e automatizadas para avaliar o desempenho de um modelo no Portal da Fábrica de IA do Azure.

Este exercício levará aproximadamente **30** minutos.

> **Observação**: algumas das tecnologias usadas neste exercício estão em versão prévia ou em desenvolvimento ativo. Você pode observar algum comportamento, avisos ou erros inesperados.

## Crie um hub e projeto da Fábrica de IA do Azure

Os recursos da Fábrica de IA do Azure que usaremos neste exercício requerem um projeto baseado em um recurso de *hub* da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir (feche o painel **Ajuda** se estiver aberto):

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. No navegador, navegue até `https://ai.azure.com/managementCenter/allResources` e clique em **Criar**. Em seguida, escolha a opção para criar um novo **Recurso do hub de IA**.
1. No assistente **Criar um projeto**, insira um nome válido para o projeto e use o link **Renomear hub**, para especificar um nome válido para o novo hub. Expanda **Opções avançadas** e especifique as seguintes configurações para o seu projeto:
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Região**: selecione um dos seguintes locais (*Caso um limite de cota seja excedido posteriormente no exercício, talvez você precise criar outro recurso em uma região diferente.*):
        - Leste dos EUA 2
        - França Central
        - Sul do Reino Unido
        - Suécia Central

    > **Observação**: se você estiver trabalhando em uma assinatura do Azure na qual as políticas são usadas para restringir nomes de recursos permitidos, talvez seja necessário usar o link na parte inferior da caixa de diálogo **Criar um novo projeto** para criar o hub usando o portal do Azure.

    > **Dica**: se o botão **Criar** ainda estiver desativado, certifique-se de renomear seu hub para um valor alfanumérico exclusivo.

1. Aguarde até que seu projeto seja criado.

## Implantar modelos

Neste exercício, você avaliará o desempenho de um modelo gpt-4o-mini. Você também usará um modelo gpt-4o para gerar métricas de avaliação assistidas por IA.

1. No painel de navegação à esquerda do seu projeto, na seção **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Na página **Modelos + pontos extremidades**, na guia **Implantações de modelo**, no menu **+ Implantar modelo**, selecione **Implantar modelo base**.
1. Procure o modelo **gpt-4o** na lista, selecione-o e confirme-o.
1. Crie uma nova implantação do modelo com as seguintes configurações selecionando **Personalizar** nos detalhes de implantação:
    - **Nome da implantação**: *Um nome válido para a implantação de modelo*
    - **Tipo de implantação**: padrão global
    - **Atualização automática de versão**: Ativado
    - **Versão do modelo**: *selecione a versão mais recente disponivel*
    - **Recurso de IA conectado**: *selecione a sua conexão de recursos do OpenAI do Azure*
    - **Limite de taxa de tokens por minuto (milhares):** 50 mil *(ou o máximo disponível em sua assinatura, se inferior a 50 mil)*
    - **Filtro de conteúdo**: DefaultV2

    > **Observação**: A redução do TPM ajuda a evitar o uso excessivo da cota disponível na assinatura que você está usando. 50.000 TPM são suficientes para os dados usados neste exercício. Se a sua cota disponível for menor do que isso, você poderá concluir o exercício, mas poderá ocorrer erros se o limite de taxa for excedido.

1. Aguarde até que a implantação seja concluída.
1. Retorne à página **Modelos + pontos de extremidade** e repita as etapas anteriores para implantar um modelo **gpt-4o-mini** com as mesmas configurações.

## Avaliar manualmente um modelo

Você pode revisar manualmente as respostas do modelo com base nos dados do teste. A revisão manual permite testar diferentes entradas para avaliar se o modelo está funcionando conforme o esperado.

1. Em uma nova guia do navegador, baixe [travel_evaluation_data.jsonl](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl) em `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl` e salve em uma pasta local como **travel_evaluation_data.jsonl** (salve como um arquivo .jsonl, não como um arquivo .txt).
1. De volta à guia do portal da Fábrica de IA do Azure, no painel de navegação, na seção **Proteger e controlar**, clique em **Avaliação**.
1. Se o painel **Criar uma nova avaliação** abrir automaticamente, clique em **Cancelar** para fechá-lo.
1. Na página **Avaliação**, visualize a guia **Avaliações manuais** e selecione **+ Nova avaliação manual**.
1. Na seção **Configurações**, na lista **Modelo**, selecione a sua implantação do modelo **gpt-4o**.
1. Altere a **Mensagem do sistema** para as seguintes instruções de um assistente de viagens de IA:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Na seção **Resultado de avaliação manual**, clique em **Importar dados de testes** e faça upload do arquivo **travel_evaluation_data.jsonl** rolando para baixo para mapear os campos do conjunto de dados da seguinte maneira:
    - **Entrada**: Question
    - **Resposta esperada**: ExpectedResponse
1. Examine as perguntas e as respostas esperadas no arquivo de teste — você as usará para avaliar as respostas geradas pelo modelo.
1. Selecione **Executar** na barra superior para gerar saídas para todas as perguntas que você adicionou como entradas. Após alguns minutos, as respostas do modelo devem ser mostradas em uma nova coluna **Saída**, como esta:

    ![Captura de tela de uma página de avaliação manual no Portal da Fábrica de IA do Azure.](./media/manual-evaluation.png)

1. Revise as saídas de cada pergunta, comparando a saída do modelo com a resposta esperada e "pontuando" os resultados selecionando o ícone de polegar para cima ou para baixo no canto inferior direito de cada resposta.
1. Depois de pontuar as respostas, examine os blocos de resumo acima da lista. Em seguida, na barra de ferramentas, selecione **Salvar resultados** e atribua um nome adequado. Salvar os resultados permite recuperá-los posteriormente para avaliação ou comparação com um modelo diferente.

## Usar avaliações automatizadas

Embora comparar manualmente a saída do modelo com suas próprias respostas esperadas possa ser uma maneira útil de avaliar o desempenho de um modelo, é uma abordagem demorada em cenários em que você espera uma ampla variedade de perguntas e respostas e fornece pouco em termos de métricas padronizadas que você pode usar para comparar diferentes combinações de modelos e prompts.

A avaliação automatizada é uma abordagem que tenta resolver essas deficiências calculando métricas e usando IA para avaliar as respostas quanto à coerência, relevância e outros fatores.

1. Use a seta para trás (**&larr;**) ao lado do título da página **Avaliação manual** para retornar à página **Avaliação**.
1. Exiba a guia **Avaliações automatizadas**.
1. Clique em **Criar uma nova avaliação** e, quando solicitado, selecione a opção para **Avaliar um modelo** e clique em **Avançar**.
1. Na página **Selecionar fonte de dados**, clique em **Usar seu conjunto de dados** e selecione o conjunto de dados **travel_evaluation_data_jsonl_*xxxx...*** com base no arquivo que você carregou anteriormente e clique em **Avançar**.
1. Na página **Testar modelo**, selecione o modelo **gpt-4o-mini** e altere a **Mensagem do sistema** para as mesmas instruções de um assistente de viagem de IA que você usou anteriormente:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. No campo **consulta**, selecione **\{\{item.question\}\}**.
1. Selecione **Próximo** para ir para a próxima página.
1. Na página **Configurar avaliadores**, use o botão **+Adicionar** para adicionar os seguintes avaliadores, configurando cada um da seguinte maneira:
    - **Marcador de modelo**:
        - **Nome do critério**: Semantic_similarity
        - **Classificar com**: *selecione o seu modelo **gpt-4o***
        - Configurações de **usuário** (na parte inferior):


            Saída: \{\{sample.output_text\}\}<br>
            Verdade básica: \{\{item.ExpectedResponse\}\}<br>
            <br>
        
    - **Avaliador de escala Likert**:
        - **Nome do critério**: Relevância
        - **Classificar com**: *selecione o seu modelo **gpt-4o***
        - **Consulta**: \{\{item.question\}\}

    - **Semelhança de texto**:
        - **Nome do critério**: F1_Score
        - **Verdade básica**: \{\{item.ExpectedResponse\}\}

    - **Conteúdo de ódio e injusto**:
        - **Nome do critério**: Hate_and_unfairness
        - **Consulta**: \{\{item.question\}\}

1. Clique em **Avançar** para revisar as configurações de avaliação. Você deve ter configurado a avaliação para usar o conjunto de dados de avaliação de viagens para avaliar o modelo **gpt-4o-mini** quanto à semelhança semântica, relevância, pontuação F1 e linguagem de ódio e injustiça.
1. Dê um nome adequado à avaliação, clique em **Enviar** para iniciar o processo de avaliação e aguarde a conclusão. Isso pode levar alguns minutos. Você pode usar o botão da barra de ferramentas **Atualizar** para verificar o status.

1. Quando a avaliação for concluída, role para baixo, se necessário, para revisar os resultados.

    ![Captura de tela das métricas de avaliação.](./media/ai-quality-metrics.png)

1. Na parte superior da página, clique na guia **Dados** para ver os dados brutos da avaliação. Os dados incluem as métricas para cada entrada, bem como explicações do raciocínio que o modelo gpt-4o aplicou ao avaliar as respostas.

## Limpar

Quando terminar de explorar o Azure IA Foundry, exclua os recursos criados para evitar custos desnecessários do Azure.

- Navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`.
- No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
- Selecione o grupo de recursos criado para este exercício.
- Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
- Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
