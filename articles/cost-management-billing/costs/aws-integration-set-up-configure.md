---
title: Configurar a integração do AWS com o Azure Cost Management
description: Este artigo acompanha-o através da definição e configuração da integração do relatório de Custos e Utilização do AWS com o Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 2b8a008decc41a5686fb2c8d9fee271f95f0fef3
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122408"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Definir e configurar a integração do relatório de Custos e Utilização do AWS

Com a integração do relatório de Custos e Utilização (CUR) do Amazon Web Services (AWS), pode monitorizar e controlar os gastos do AWS no Azure Cost Management. A integração oferece uma única localização no portal do Azure, onde monitoriza e controla as despesas do Azure e do AWS. Este artigo explica como configurar a integração e como a definir para que possa utilizar as funcionalidades do Azure Cost Management para analisar os custos e os orçamentos.

O Cost Management processa o relatório de Custos e Utilização do AWS armazenado num registo S3 com as suas credenciais de acesso ao AWS para obter as definições do relatório e transferir os ficheiros CSV do relatório no formato GZIP.

Veja o vídeo [How to set up Connectors for AWS in Cost Management](https://www.youtube.com/watch?v=Jg5KC1cx5cA) (Como configurar Conectores para o AWS no Cost Management) para saber mais sobre como configurar a integração de relatórios do AWS. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>Criar um relatório de Custos e Utilização no AWS

A utilização de um relatório de Custos e Utilização é a forma recomendada pelo AWS para recolher e processar os custos do AWS. Para obter mais informações, veja a documentação do [AWS Cost and Usage Report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) (Relatório de Custos e Utilização do AWS).

Utilize a página **Relatórios de Custos e Utilização** da consola Faturação e Cost Management no AWS para criar um relatório de Custos e Utilização com os seguintes passos:

1. Inicie sessão na Consola de Gestão do AWS e abra a [consola Faturação e Cost Management](https://console.aws.amazon.com/billing).
2. No painel de navegação, selecione **Relatórios de Custos e Utilização**.
3. Selecione **Criar relatório**.
4. Em **Nome do relatório**, introduza um nome para o relatório.
5. Em **Detalhes adicionais do relatório**, selecione **Incluir IDs dos recursos**.
6. Em **Definições de atualização dos dados**, selecione se quer que o relatório de Custos e Utilização do AWS seja atualizado quando o AWS aplicar reembolsos, créditos ou taxas de suporte na sua conta após finalizar a fatura. Quando um relatório é atualizado, é carregado um novo relatório para o Amazon S3. Recomendamos que deixe esta definição selecionada.
7. Selecione **Seguinte**.
8. Para **Registo S3**, escolha **Configurar**.
9. Na caixa de diálogo de Registo Configurar S3 introduza um nome de registo, a Região onde quer criar o novo registo e escolha **Seguinte**.
10. Selecione **Confirmei que esta política está correta** e clique em **Guardar**.
11. (Opcional) No prefixo Caminho do Relatório, introduza o prefixo do caminho do relatório que quer adicionar ao nome do relatório.
Se não especificar um prefixo, o prefixo predefinido é o nome que especificou para o relatório. O intervalo de datas tem o formato `/report-name/date-range/`.
12. Para **Unidade de tempo**, escolha **Horas**.
13. Para **Versões do relatório**, escolha se quer que cada versão do relatório substitua a versão anterior ou se quer novos relatórios adicionais.
14. Para **Permitir integração de dados para**, não é necessária nenhuma seleção.
15. Para **Compressão**, selecione **GZIP**.
16. Selecione **Seguinte**.
17. Após analisar as definições do relatório, selecione **Rever e Concluir**.

    Anote o nome do relatório. Será utilizado em passos posteriores.

Pode demorar até 24 horas para o AWS começar a entregar relatórios no seu registo Amazon S3. Após o início da entrega, o AWS atualiza os ficheiros do relatório de Custos e Utilização do AWS pelo menos uma vez por dia. Pode continuar a configurar o seu ambiente do AWS sem esperar que a entrega seja iniciada.

## <a name="create-a-role-and-policy-in-aws"></a>Criar uma função e uma política no AWS

O Azure Cost Management acede várias vezes por dia ao registo S3 onde o relatório de Custo e Utilização está localizado. O serviço precisa de ter acesso às credenciais para verificar se existem dados novos. Cria uma função e uma política no AWS para permitir o acesso do Cost Management.

Para ativar o acesso baseado em funções a uma conta do AWS no Cost Management, a função deverá ser criada na consola do AWS. Precisa de ter o _ARN da função_ e o _ID externo_ da consola do AWS. Posteriormente, serão utilizados na página **Criar um conector AWS** do Cost Management.

Utilize o assistente Criar uma Nova Função:

1. Inicie sessão na consola do AWS e selecione **Serviços**.
2. Na lista de serviços, selecione **IAM**.
3. Selecione **Funções** e, em seguida, **Criar Função**.
4. Na página seguinte, selecione **Outra conta do AWS**.
5. Em **ID de conta**, introduza **432263259397**.
6. Em **Opções**, selecione **Exigir ID externo (Melhor prática se a função for assumida por terceiros)** .
7. Em **ID externo**, introduza o ID externo que é um código de acesso partilhado entre a função do AWS e o Azure Cost Management. O mesmo ID externo é também utilizado na página **Novo Conector** do Cost Management. A Microsoft recomenda que utilize uma política de código de acesso forte ao introduzir o ID externo.
    > [!NOTE]
    > Não altere a seleção de **Requerer MFA**. Esta opção deve permanecer desmarcada.
8. Selecione **Seguinte: Permissões**.
9. Selecione **Criar política**. É aberto um novo separador do browser. É aqui que vai criar a política.
10. Selecione **Escolher um serviço**.

Configure as permissões para o relatório de Custos e Utilização:

1. Introduza **Relatório de Custos e Utilização**.
2. Selecione **Nível de acesso** > **Leitura** > **DescribeReportDefinitions**. Este passo permite ao Cost Management ler quais os relatórios CUR definidos e determinar se correspondem ao pré-requisito de definição de relatórios.
3. Selecione **Adicionar mais permissões**.

Configure a permissão para o registo S3 e os objetos:

1. Selecione **Escolher um serviço**.
2. Introduza **S3**.
3. Selecione **Nível de acesso** > **Lista** > **ListBucket**. Esta ação obtém a lista de objetos no Registo S3.
4. Selecione **Nível de acesso** > **Leitura** > **GetObject**. Esta ação permite a transferência dos ficheiros de faturação.
5. Selecione **Recursos**.
6. Selecione **registo – Adicionar ARN**.
7. Em **Nome do registo**, introduza o registo utilizado para armazenar os ficheiros CUR.
8. Selecione **objeto – Adicionar ARN**.
9. Em **Nome do registo**, introduza o registo utilizado para armazenar os ficheiros CUR.
10. Em **Nome do objeto**, selecione **Qualquer**.
11. Selecione **Adicionar mais permissões**.

Configure a permissão para o Explorador de Custos:

1. Selecione **Escolher um serviço**.
2. Introduza **Serviço do Explorador de Custos**.
3. Selecione **Todas as ações do Serviço do Explorador de Custos (ce:\*)** . Esta ação confirma que a coleção está correta.
4. Selecione **Adicionar mais permissões**.

Adicionar permissão para as Organizações do AWS:

1. Introduza **Organizações**.
2. Selecione **Nível de acesso** > **Lista** > **ListAccounts**. Esta ação obtém os nomes das contas.
3. Em **Política de Revisão**, introduza um nome para a nova política. Confirme se introduziu as informações corretas e selecione **Criar Política**.
4. Volte ao separador anterior e atualize a página Web do browser. Na barra de pesquisa, procure a nova política.
5. Selecione **Seguinte: Revisão**.
6. Introduza um nome para a nova função. Confirme se introduziu as informações corretas e selecione **Criar Função**.

    Anote o ARN da função e o ID externo utilizados nos passos anteriores quando criou a função. Serão utilizados posteriormente quando configurar o conector do Azure Cost Management.

A política JSON deve assemelhar-se ao seguinte exemplo. Substitua _bucketname_ pelo nome do registo S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
             "ce:*",
             "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>Configurar um novo conector para o AWS no Azure

Utilize as seguintes informações para criar um conector AWS e começar a monitorizar os custos do AWS:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Aceda à home page do Azure ao clicar em **Home** no menu do lado esquerdo (menu de "hambúrguer" com três linhas).
3. Aceda a **Ferramentas** > **Cost Management** na parte inferior da página.
3. Em **Definições**, selecione **Conectores para o AWS**.  
4. Selecione **+Adicionar** na parte superior da página para criar um conector.  
    :::image type="content" source="./media/aws-integration-setup-configure/aws-connector.png" alt-text="Exemplo a mostrar a definição Conectores para o AWS" :::
1. Na página **Criar um conector** em **Nome a apresentar**, introduza um nome para o conector.  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="Exemplo da página para criar um conector AWS" :::
1. Opcionalmente, selecione o grupo de gestão predefinido. Armazenará todas as contas associadas detetadas. Poderá ser configurado mais tarde.
1. Na secção **Faturação**, selecione **Renovação automática** para **Ativada** se quiser garantir a operação contínua. Se selecionar a opção automática, terá de selecionar uma subscrição de faturação.
1. Para **ARN da função**, introduza o valor que utilizou quando configurou a função no AWS.
1. Para **ID externo**, introduza o valor que utilizou quando configurou a função no AWS.
1. Para **Nome de Relatório**, introduza o nome que criou no AWS.
1. Selecione **Seguinte** e, em seguida, **Criar**.

A apresentação dos novos âmbitos do AWS, da conta consolidada do AWS, das contas associadas do AWS e dos dados dos custos pode demorar algumas horas.

Depois de criar o conector, recomendamos que lhe atribua o controlo de acesso. São atribuídas aos utilizadores permissões para os âmbitos recém-detetados: Conta consolidada do AWS e contas associadas do AWS. O utilizador que cria o conector é o proprietário do conector, da conta consolidada e de todas as contas associadas.

A atribuição das permissões do conetor aos utilizadores após ocorrer a deteção não atribui permissões aos âmbitos existentes do AWS. Em vez disso, são atribuídas permissões apenas às novas contas associadas.

## <a name="take-additional-steps"></a>Passos adicionais

- [Configure grupos de gestão](../../governance/management-groups/overview.md#initial-setup-of-management-groups) se ainda não o tiver feito.
- Confirme que são adicionados novos âmbitos ao seletor de âmbito. Selecione **Atualizar** para ver os dados mais recentes.
- Na página **Conectores da cloud**, selecione o seu conector e selecione **Ir para a conta de faturação** para atribuir a conta associada a grupos de gestão.

> [!NOTE]
> Atualmente, os grupos de gestão não são suportados para clientes do Contrato de Cliente Microsoft (MCA). Os clientes do MCA podem criar o conector e ver os respetivos dados do AWS. No entanto, os clientes do MCA não podem ver os custos do Azure e os custos do AWS juntos num grupo de gestão.

## <a name="manage-aws-connectors"></a>Gerir conectores do AWS

Quando seleciona um conector na página **Conectores para o AWS**, pode:

- Selecionar **Ir para Conta de Faturação** para ver as informações da conta consolidada do AWS.
- Selecionar **Controlo de Acesso** para gerir a atribuição de funções do conector.
- Selecione **Editar** para atualizar o conector. Não pode alterar o número de conta do AWS, dado que este aparece no ARN da função. Mas, pode criar um novo conector.
- Selecionar **Verificar** para executar novamente o teste de verificação para garantir que o Cost Management consegue recolher dados com as definições do conector.

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="Detalhes do conector do AWS de exemplo" :::

## <a name="set-up-azure-management-groups"></a>Configurar grupos de gestão do Azure

Coloque as suas subscrições do Azure e contas associadas do AWS no mesmo grupo de gestão para criar uma única localização onde possa ver as informações do fornecedor entre clouds. Se ainda não tiver configurado o seu ambiente do Azure com grupos de gestão, veja [Configuração inicial dos grupos de gestão](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Se quiser separar os custos, poderá criar um grupo de gestão que possua apenas as contas associadas do AWS.

## <a name="set-up-an-aws-consolidated-account"></a>Configurar uma conta consolidada do AWS

A conta consolidada do AWS combina a faturação e o pagamento de várias contas AWS. Também age como uma conta associada do AWS. Pode ver os detalhes da sua conta consolidada do AWS através da hiperligação na página do conector do AWS. 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="Exemplo que detalha uma conta consolidada do AWS" :::

Nesta página, pode:

- Selecionar **Atualizar** para atualizar em massa a associação de contas associadas do AWS com um grupo de gestão.
- Selecionar **Controlo de Acesso** para definir a atribuição de funções do âmbito.

### <a name="permissions-for-an-aws-consolidated-account"></a>Permissões para uma conta consolidada do AWS

Por predefinição, as permissões para uma conta consolidada do AWS são definidas aquando da criação da conta com base nas permissões do conector AWS. O criador do conector é o proprietário.

Pode gerir o nível de acesso na página **Nível de Acesso** da conta consolidada do AWS. No entanto, as contas associadas do AWS não herdam permissões para a conta consolidada do AWS.

## <a name="set-up-an-aws-linked-account"></a>Configurar uma conta associada do AWS

A conta associada do AWS é onde os recursos do AWS são criados e geridos. Uma conta associada também funciona como um limite de segurança.

Nesta página, pode:

- Selecionar **Atualizar** para atualizar a associação de uma conta associada do AWS com um grupo de gestão.
- Selecionar **Controlo de Acesso** para definir uma atribuição de funções do âmbito.

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="Exemplo da página Conta Associada do AWS" :::

### <a name="permissions-for-an-aws-linked-account"></a>Permissões para uma conta associada do AWS

Por predefinição, as permissões para uma conta associada do AWS são definidas aquando da criação, com base nas permissões do conector AWS. O criador do conector é o proprietário. Pode gerir o nível de acesso na página **Nível de Acesso** da conta associada do AWS. No entanto, as contas associadas do AWS não herdam as permissões de uma conta consolidada do AWS.

As contas associadas do AWS herdam sempre as permissões do grupo de gestão a que pertencem.

## <a name="next-steps"></a>Passos seguintes

- Agora que definiu e configurou a integração do relatório de Custos e Utilização do AWS, continue para [Gerir os custos e a utilização do AWS](aws-integration-manage.md).
- Se não estiver familiarizado com a análise de custos, veja o início rápido [Explorar e analisar os custos com a análise de custos](quick-acm-cost-analysis.md).
- Se não estiver familiarizado com os orçamentos no Azure, veja [Criar e gerir orçamentos do Azure](tutorial-acm-create-budgets.md).
