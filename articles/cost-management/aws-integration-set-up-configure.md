---
title: Definir e configurar a integração de relatório de utilização e custos do AWS com o Azure Cost Management
description: Este artigo orienta-definir e configurar a integração de relatório de utilização e custos do AWS com o Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002133"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Definir e configurar a integração de relatório de custos do AWS e utilização

Com o Amazon Web Services (AWS) custo e a utilização de integração de relatório (ATUAIS), pode monitorizar e controlar os gastos de AWS no Azure Cost Management. A integração permite que uma única localização no portal do Azure onde pode monitorizar e controle de gastos do Azure e AWS. Este artigo explica como configurar a integração e configurá-la para que usar recursos de gestão de custos do Azure para analisar os custos e orçamentos de rever.

Processos de gestão de custos o relatório de custos do AWS e utilização armazenado no registo de S3 utilizando suas credenciais de acesso do AWS para obter definições de relatórios e transferir ficheiros CSV de GZIP de relatório.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Criar um relatório de custo e a utilização no AWS

Através de um relatório de custo e a utilização é a forma recomendada de AWS para coletar e processar os custos do AWS. Para obter mais informações, consulte a [custos do AWS e o relatório de utilização](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) documentação.

Utilize o **relatórios de utilização e de custo** página da consola de faturação e gestão de custos no AWS para criar um relatório de custo e a utilização com os seguintes passos:

1. Inicie sessão na consola de gestão do AWS e abra o [consola de gestão de custos e de faturação](https://console.aws.amazon.com/billing).
2. No painel de navegação, selecione **relatórios de utilização e de custo**.
3. Selecione **criar relatório**.
4. Para **nome do relatório**, introduza um nome para o relatório.
5. Para **detalhes do relatório adicional** , para incluir os IDs de cada recurso no relatório e selecione **incluir IDs de recurso**.
6. Para **definições de atualização de dados**, selecione se pretende que o relatório de utilização e custos do AWS para atualizar se AWS aplica-se reembolsos, créditos, ou suportar taxas para a sua conta após a finalizar a sua fatura. Quando atualiza um relatório, um novo relatório é carregado para o Amazon S3. É recomendável deixar esta definição no.
7. Selecione **Seguinte**.
8. Para **registo de S3**, escolha **configurar**.
9. Na caixa de diálogo Configurar registo de S3, efetue um dos seguintes procedimentos:
    1. Selecione um registo existente na lista pendente e escolha **seguinte**.
    2. Introduza um nome de registo e a região onde pretende criar um novo registo e escolha **seguinte**.
10. Selecione que eu tiver confirmado que esta política é corrige e selecione guardar.
11. (Opcional) Para o prefixo do caminho de relatório, introduza o prefixo do caminho de relatório que pretende que o antecedendo para o nome do seu relatório.
Se não especificar um prefixo, o prefixo predefinido é o nome que especificou para o relatório no passo 4 e o intervalo de datas para o relatório, no seguinte formato: `/report-name/date-range/`
12. Para **unidade de tempo**, escolha **por hora**.
13. Para **controle de versão do relatório**, escolha se pretende que cada versão do relatório para substituir a versão anterior do relatório ou para ser entregue para além das versões anteriores.
14. Para **ativar a integração de dados para**, nenhuma seleção é necessária.
15. Para **compressão**, selecione **GZIP**.
16. Selecione **Seguinte**.
17. Após ter examinado as definições para o relatório, selecione **revisão e concluída**.

    Tenha em atenção o nome do relatório. Irá utilizá-lo em passos posteriores.

Pode demorar até 24 horas para AWS começar a entrega de relatórios para o bucket do Amazon S3. Após a entrega é iniciado, o AWS atualiza os ficheiros de relatório de custos do AWS e utilização, pelo menos, uma vez por dia. Pode continuar a configurar o seu ambiente de AWS sem esperar pela entrega iniciar.

## <a name="create-a-role-and-policy-in-aws"></a>Criar uma função e a política no AWS

O Azure Cost Management acessa o registo de S3 onde o relatório de custo e a utilização se encontra várias vezes ao dia. O serviço precisa de aceder às credenciais para verificar a existência de novos dados. Criar uma função e a política no AWS para permitir a gestão de custos para aceder ao mesmo.

Para ativar o acesso baseado em funções para uma conta AWS no Cost Management, a função é criada na consola do AWS. Tem de ter o _função ARN_ e _ID externo_ a partir da consola do AWS. Mais tarde, utilizá-los sobre o **criar um conector do AWS** página no Cost Management.

Utilize o Assistente de criação de nova função:

1. Inicie sessão na sua consola AWS e selecione **serviços**.
2. Na lista de serviços, selecione **IAM**.
3. Selecione **funções** e, em seguida, selecione **criar função**.
4. Na página seguinte, selecione **conta AWS outro**.
5. Na **ID da conta**, introduza **432263259397**.
6. Na **opções**, selecione **necessitam de ID externo (melhor prática, quando uma aplicação de terceiros irão assumir essa função)** .
7. Na **ID externo**, introduza o ID externo. O ID externo é um código de acesso partilhado entre a função AWS e o Azure Cost Management. O mesmo ID externo é também usado na **novo conector** página no Cost Management. Por exemplo, é semelhante a um ID externo _Companyname1234567890123_.

    > [!NOTE]
    > Não alterar a seleção para **exigir MFA**. Deve permanecer desmarcada.
8. Selecione **seguinte: Permissões**.
9. Selecione **criar política**. É aberto um novo separador do browser. Que é onde cria uma política.
10. Selecione **escolher um serviço**.

Configure as permissões para o relatório de custo e a utilização:

1. Introduza **custo e o relatório de utilização**.
2. Selecione **nível de acesso** > **leitura** > **DescribeReportDefinitions**. Este passo permite a gestão de custos ler os relatórios que ATUAIS são definidos e determinar se eles correspondem o pré-requisito de definição de relatório.
3. Selecione **adicionar permissões adicionais**.

Configure as permissões para o seu registo de S3 e objetos:

1. Selecione **escolher um serviço**.
2. Enter **S3**.
3. Selecione **nível de acesso** > **lista** > **ListBucket**. Esta ação obtém a lista de objetos no registo de S3.
4. Selecione **nível de acesso** > **leitura** > **GetObject**. Esta ação permite a transferência de ficheiros de faturas.
5. Selecione **recursos**.
6. Selecione **bucket – adicionar ARN**.
7. Na **nome do Bucket**, introduza o registo utilizado para armazenar os ficheiros ATUAIS.
8. Selecione **objeto – adicionar ARN**.
9. Na **nome do Bucket**, introduza o registo utilizado para armazenar os ficheiros ATUAIS.
10. Na **nome do objeto**, selecione **qualquer**.
11. Selecione **adicionar permissões adicionais**.

Configure as permissões para o Explorer de custo:

1. Selecione **escolher um serviço**.
2. Introduza **custo Explorer Service**.
3. Selecione **ações de todos os serviços de Explorador de custo (ce:\*)** . Esta ação valida que a coleção está correta.
4. Selecione **adicionar permissões adicionais**.

Adicione permissão para organizações de AWS:

1. Introduza **organizações**.
2. Selecione **nível de acesso** > **lista** > **ListAccounts**. Esta ação obtém os nomes das contas.
3. Na **rever política**, introduza um nome para a nova política. Verifique se introduziu as informações corretas e, em seguida, selecione **criar política**.
4. Volte ao separador anterior e atualizar a página Web do seu browser. Na barra de pesquisa, procure a sua nova política.
5. Selecione **próxima: revisão**.
6. Introduza um nome para a nova função. Verifique se introduziu as informações corretas e, em seguida, selecione **criar função**.

    Tenha em atenção a função ARN e o ID externo utilizado nos passos anteriores quando criou a função. Usará-los mais tarde quando configurar o conector do Azure Cost Management.

O JSON de política deve ser semelhante ao seguinte exemplo. Substitua _bucketname_ com o nome do seu registo de S3.

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

## <a name="set-up-a-new-aws-connector-in-azure"></a>Configurar um novo conector AWS no Azure

Utilize as seguintes informações para criar um conector do AWS e começar a monitorizar os seus custos do AWS:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Aceda a **custo Management + faturação** > **gestão de custos**.
3. Sob **configurações**, selecione **conectores (pré-visualização) da Cloud**.  
    ![Exemplo que mostra os conectores de nuvem (pré-visualização) definição)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Selecione **+ adicionar** na parte superior da página para criar um conector.
5. Sobre o **criar um conector do AWS** na página **nome a apresentar**, introduza um nome para o conector.  
    ![Exemplo da página para a criação de um conector do AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Opcionalmente, selecione o grupo de gestão predefinido. Irá armazenar todos os detetados contas ligadas. Pode configurá-lo mais tarde.
7. Na **faturação** secção, selecione **automaticamente a cobrar o 1% na disponibilidade geral** se pretender certificar-se a operação contínua quando expira a pré-visualização. Se selecionar a opção automática, tem de selecionar uma subscrição de faturação.
8. Para **função ARN**, introduza o valor que utilizou quando configurou a função no AWS.
9. Para **ID externo**, introduza o valor que utilizou quando configurou a função no AWS.
10. Para **nome do relatório**, introduza o nome que criou no AWS.
11. Selecione **próxima** e, em seguida, selecione **criar**.

Poderá demorar algumas horas para os novos âmbitos do AWS, o AWS consolidados conta e contas AWS ligado e seus dados de custo a aparecer.

Depois de criar o conector, recomendamos que atribua o controlo de acesso a ele. Os utilizadores estão a receber permissões para os âmbitos detetados recentemente: AWS consolidados conta e contas AWS ligado. O utilizador que cria o conector é o proprietário do conector, a conta consolidada e todas as contas ligadas.

Atribuir permissões dos conectores aos usuários após a ocorrência de deteção não atribui permissões para os âmbitos AWS existentes. Em vez disso, apenas os novos contas ligadas são atribuídas permissões.

## <a name="take-additional-steps"></a>Ações adicionais

- [Configurar grupos de gestão](../governance/management-groups/index.md#initial-setup-of-management-groups), se ainda não o fez.
- Verifique que os novos âmbitos são adicionados ao seu Seletor de âmbito. Selecione **atualizar** para ver os dados mais recentes.
- Sobre o **conectores da Cloud** página, selecione o conector e selecione **vá para a conta de cobrança** para atribuir a conta associada a grupos de gestão.

## <a name="manage-cloud-connectors"></a>Gerir conectores de cloud

Quando seleciona um conector no **Cloud conectores** página, pode:

- Selecione **vá para a conta de cobrança** para ver as informações de que o AWS consolidados conta.
- Selecione **controlo de acesso** para gerir a atribuição de função para o conector.
- Selecione **editar** para atualizar o conector. Não é possível alterar o número da conta AWS, uma vez que aparece na função ARN. Mas pode criar um novo conector.
- Selecione **Verifique se** voltar a executar o teste de verificação para certificar-se de que o Cost Management pode recolher dados com as definições do conector.

![Lista de exemplos de conectores AWS criados](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Configurar grupos de gestão do Azure

Para criar um único local para ver informações do fornecedor de entre Clouds, precisa colocar as suas subscrições do Azure e contas AWS ligado no mesmo grupo de gestão. Se já não tiver configurado o seu ambiente do Azure com grupos de gestão, consulte [inicial do programa de configuração de grupos de gestão](../governance/management-groups/index.md#initial-setup-of-management-groups).

Se pretender separar os custos, pode criar um grupo de gestão que contém apenas as contas AWS ligado.

## <a name="set-up-an-aws-consolidated-account"></a>Configurar o AWS consolidados conta

A conta AWS consolidados combina o pagamento para várias contas AWS e de faturação. Ela também atua como uma conta AWS ligado.

![Detalhes de exemplo para AWS consolidados conta](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

A partir da página, pode:

- Selecione **atualizar** para atualização em massa a associação de AWS associado a contas com um grupo de gestão.
- Selecione **controlo de acesso** para definir a atribuição de função para o âmbito.

### <a name="permissions-for-an-aws-consolidated-account"></a>Permissões para AWS consolidados conta

Por predefinição, as permissões para uma conta AWS consolidados estão definidas durante a criação da conta, com base nas permissões do conector de AWS. O criador de conector é o proprietário.

Gerir o nível de acesso utilizando o **nível de acesso** página do AWS consolidados conta. No entanto, o AWS ligado contas não herdarem permissões para a conta AWS consolidados.

## <a name="set-up-an-aws-linked-account"></a>Configurar uma conta AWS ligado

A conta AWS ligado é onde os recursos do AWS são criados e geridos. Uma conta ligada também atua como um limite de segurança.

Nesta página, pode:

- Selecione **atualizar** para atualizar a associação de AWS associado à conta com um grupo de gestão.
- Selecione **controlo de acesso** para definir uma atribuição de função para o âmbito.

![Exemplo da página de conta ligada do AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>As permissões para AWS ligadas conta

Por predefinição, as permissões para uma conta AWS ligado são definidas durante a criação, com base nas permissões do conector de AWS. O criador de conector é o proprietário. Gerir o nível de acesso utilizando o **nível de acesso** página do AWS ligado conta. AWS ligado contas não herdam as permissões de uma conta AWS consolidados.

AWS ligado contas sempre herdar permissões do grupo de gestão que pertencem.

## <a name="next-steps"></a>Passos Seguintes

- Agora que configurar e configurado a integração de relatório de utilização e custos do AWS, avance para [utilização e os custos do AWS gerir](aws-integration-manage.md).
- Se não estiver familiarizado com a análise de custo, veja [explorar e analisar os custos com a análise de custo](quick-acm-cost-analysis.md) início rápido.
- Se não estiver familiarizado com orçamentos no Azure, veja [criar e gerir o Azure orçamentos](tutorial-acm-create-budgets.md).
