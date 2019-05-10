---
title: Definir e configurar a integração de relatório de utilização e custos do AWS com o Azure Cost Management
description: Este artigo descreve o que definir e configurar a integração de relatório de utilização e custos do AWS com o Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: e87e95ec9e4e20ee4785c2b1f448a7ca5f442b8a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409200"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Definir e configurar a integração de relatório de custos do AWS e utilização

Com a integração de relatório de utilização e custo do Amazon Web Services, pode monitorizar e controlar os gastos de AWS no Azure Cost Management. A integração permite que uma única localização no portal do Azure onde pode monitorizar e controle de gastos do Azure e AWS. Este artigo explica como configurar a integração e configurá-la para que usar recursos de gestão de custos para analisar os custos e orçamentos de rever.

Processos de gestão de custos o relatório de custos do AWS e utilização armazenado no registo de S3 utilizando suas credenciais de acesso do AWS para obter definições de relatórios e transferir ficheiros CSV de GZIP de relatório.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Criar um relatório de custo e a utilização no AWS

Através de um relatório de custo e a utilização é AWS recomendado forma para coletar e processar os custos do AWS. Para obter mais informações, consulte a [custos do AWS e o relatório de utilização](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) artigo de documentação.

Utilize o **relatórios** página da consola de faturação e gestão de custos no AWS para criar um relatório de custo e a utilização com os seguintes passos.

1. Inicie sessão no Console de gerenciamento do AWS e abra a consola de gestão de custos e de faturação no https://console.aws.amazon.com/billing.
2. No painel de navegação, clique em **relatórios**.
3. Clique em **criar relatório**.
4. Para **nome do relatório**, escreva um nome para o relatório.
5. Para **unidade de tempo**, escolha **por hora**.
6. Para **inclusão**, adicione os IDs de cada recurso no relatório e selecione **IDs de recurso**.
7. Para **ativar o suporte para**, nenhuma seleção é necessária.
8. Para **definições de atualização de dados**, selecione **atualiza automaticamente os seus custos &amp; relatório de utilização quando custos são detetados durante meses anteriores com fechado faturas**.
9. Clique em **Seguinte**.
10. Para **bucket do Amazon S3**, escreva o nome do bucket do Amazon S3 onde pretende que os relatórios fornecidos para e clique em **verificar**. O registo tem de ter permissões adequadas para ser válida. Para obter mais informações sobre como adicionar permissões para o registo, consulte [Bucket de definição e permissões de acesso de objeto](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html).
11. Para **prefixo do caminho de relatório**, escreva o prefixo do caminho de relatório que pretende ser adicionado ao nome do seu relatório.
12. Para **compressão**, selecione **GZIP**.
13. Clique em **Seguinte**.
14. Após ter examinado as definições para o relatório, clique em **revisão e concluída**.
    Tenha em atenção a **nome do relatório**. Irá utilizá-lo em passos posteriores.

Pode demorar até 24 horas para AWS começar a entrega de relatórios para o bucket do Amazon S3. Após a entrega é iniciado, o AWS atualiza os ficheiros de relatório de custos do AWS e utilização, pelo menos, uma vez por dia. Pode continuar a configurar o seu ambiente de AWS sem esperar pela entrega iniciar.

## <a name="create-a-role-and-policy-in-aws"></a>Criar uma função e a política no AWS

O Azure Cost Management acessa o registo de S3 onde o relatório de custo e a utilização se encontra várias vezes ao dia. Gestão de custos tem acesso às credenciais para verificar a existência de novos dados. Criar uma função e a política no AWS para permitir o acesso ao Cost Management.

Para ativar o acesso baseado em funções para uma conta AWS no Azure Cost Management, a função é criada na consola do AWS. Tem de ter o _função ARN_ e _ID externo_ a partir da consola do AWS. Mais tarde, utilizá-los o criar uma página de conector AWS no Azure Cost Management.

Utilize o criar um novo Assistente de função:

1. Início de sessão para a consola do AWS e selecione **serviços**.
2. Na lista de serviços, selecione **IAM**.
3. Selecione **funções** e, em seguida, clique em **criar função**.
4. Na página seguinte, selecione **conta AWS outro**.
5. Na **ID da conta** , introduza _432263259397_.
6. Na **opções**, selecione **necessitam de ID externo (melhor prática, quando uma aplicação de terceiros irão assumir essa função)**.
7. Na **ID externo**, introduza o ID externo. O ID externo é um código de acesso partilhado entre a função de AWS e o Azure Cost Management. O mesmo ID externo também é usado na página novo conector no Cost Management. Por exemplo, é semelhante a um ID externo _Companyname1234567890123_.
    Não altere a seleção para **exigir MFA**. Deve permanecer desmarcada.
8. Clique em **seguinte: Permissões**.
9. Clique em **criar política**. Um novo separador do browser é aberto em que cria uma nova política.
10. Clique em **escolher um serviço**.

Configure as permissões de custo e o relatório de utilização:

1. Tipo **custo e o relatório de utilização**.
2. Selecione **nível de acesso**, **leitura** > **DescribeReportDefinitions**. Isso permite a que gestão de custos, leia o que repetir relatórios são definidos e determinar se eles correspondem o pré-requisito de definição de relatório.
3. Clique em **adicionar permissões adicionais**.

Configure a sua permissão de objetos e de registo de S3:

1. Clique em **escolher um serviço**.
2. Tipo _S3_.
3. Selecione **nível de acesso**, **lista** > **ListBucket**. Esta ação obtém a lista de objetos no registo de S3.
4. Selecione **nível de acesso**, **leitura** > **GetObject**. Esta ação permite a transferência dos ficheiros de faturação.
5. Selecione **recursos**.
6. Selecione **bucket – adicionar ARN**.
7. Na **nome do Bucket**, introduza o registo utilizado para armazenar os ficheiros ATUAIS.
8. Selecione **objeto – adicionar ARN**.
9. Na **nome do Bucket**, introduza o registo utilizado para armazenar os ficheiros ATUAIS.
10. Na **nome do objeto**, selecione **qualquer**.
11. Clique em **adicionar permissões adicionais**.

Configure as permissões de custo Explorer:

1. Clique em **escolher um serviço**.
2. Tipo _custo Explorer Service_.
3. Selecione **ações de todos os serviços de Explorador de custo (ce:\*)**. Esta ação valida que a coleção está correta.
4. Clique em **adicionar permissões adicionais**.

Adicione a permissão de organizações:

1. Tipo **organizações**.
2. Selecione **nível de acesso, lista** > **ListAccounts**. Esta ação obtém os nomes das contas.
3. Na **rever política**, introduza um nome para a nova política. Verificação para certificar-se de que introduziu as informações corretas e, em seguida, clique em **criar política**.
4. Volte ao separador anterior e atualizar a página da web do seu browser. Na barra de pesquisa, procure a nova política.
5. Selecione **próxima: revisão**.
6. Introduza um nome para a nova função. Verificação para certificar-se de que introduziu as informações corretas e, em seguida, clique em **criar função**.
    Tenha em atenção a **função ARN** e o **ID externo** utilizado nos passos anteriores, quando criou a função. Irá utilizá-los mais tarde quando configurar o conector do Azure Cost Management.

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

Utilize as seguintes informações para criar um novo conector do AWS e começar a monitorizar os seus custos do AWS.

1. Iniciar sessão no portal do Azure em https://portal.azure.com.
2. Navegue para **custo Management + faturação** > **gestão de custos**.
3. Sob **configurações**, clique em **conectores (pré-visualização) da Cloud**.  
    ![Exemplo que mostra os conectores de Cloud (definição de pré-visualização)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Clique em **+ adicionar** na parte superior da página para criar um novo conector.
5. No criar uma página de conector do AWS, escreva um **nome a apresentar** para nomear o seu conector.  
    ![Exemplo do criar uma página de conector de AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Opcionalmente, selecione o grupo de gestão predefinido. Irá armazenar todos os detetados contas ligadas. Pode configurá-lo mais tarde.
7. Sob o **faturação** secção, selecione **automaticamente a cobrar o 1% na disponibilidade geral** se pretender certificar-se a operação contínua quando expira a pré-visualização. Se selecionar a opção automática, tem de selecionar uma faturação **subscrição**.
8. Introduza o **função ARN**. É o valor que utilizou quando configurou a função no AWS.
9. Introduza o **ID externo**. É o valor que utilizou quando configurou a função no AWS.
10. Introduza o **nome do relatório** que criou no AWS.
11. Clique em **próxima** e, em seguida, clique em **criar**.

Poderá demorar algumas horas para os novos âmbitos AWS, a conta do AWS consolidados e contas ligadas do AWS e seus dados de custo apareça.

Depois de criar o conector, recomendamos que atribua o controlo de acesso para o conector. Os utilizadores estão a receber permissões para os âmbitos detetados recentemente: AWS consolidados conta e o AWS ligado contas. O utilizador que cria o conector é o proprietário do conector, conta consolidada e contas de todos os ligadas.

Atribuir permissões dos conectores aos usuários após a ocorrência de deteção não atribui permissões para os âmbitos AWS existentes. Em vez disso, apenas os novos contas ligadas são atribuídas permissões.

## <a name="additional-steps"></a>Passos adicionais

- [Configurar grupos de gestão](../governance/management-groups/index.md#initial-setup-of-management-groups), se ainda não o fez.
- Verifique que os novos âmbitos são adicionados ao seu Seletor de âmbito. Não se esqueça de clicar **atualizar** para ver os dados mais recentes.
- Na página de conector em nuvem, selecione o conector e clique em **vá para a conta de cobrança** para atribuir a conta associada a grupos de gestão.

## <a name="manage-cloud-connectors"></a>Gerir conectores de cloud

Quando seleciona um conector na página de conectores de Cloud, pode:

- Clique em **vá para a conta de cobrança** para ver informações de conta consolidados do AWS.
- Clique em **controlo de acesso** para gerir a atribuição de função para o conector.
- Clique em **editar** para atualizar o conector. Não é possível alterar o número de conta AWS, tal como aparece no ARN a função. No entanto, pode criar um novo conector.
- Clique em **Verifique se** voltar a executar o teste de verificação para certificar-se de que o Cost Management pode recolher dados com as definições do conector.

![Exemplo que mostra a lista de conectores AWS criados](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Função de grupos de gestão do Azure

Para criar um único local para ver informações do fornecedor de entre Clouds, precisa colocar as suas subscrições do Azure e contas AWS ligado no mesmo grupo de gestão. Se já não tiver configurado o seu ambiente do Azure com grupos de gestão, consulte [inicial do programa de configuração de grupos de gestão](../governance/management-groups/index.md#initial-setup-of-management-groups).

Se pretender separar os custos, pode criar um grupo de gestão que contém apenas as contas AWS ligado.

## <a name="aws-consolidated-account"></a>Conta AWS consolidados

A conta AWS consolidados é utilizada para consolidar o pagamento para várias contas AWS e de faturação. Sua conta AWS consolidados também atua como uma conta AWS ligado.

![Exemplo de detalhes da conta de consolidados de AWS](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

A partir da página pode:

- Clique em **atualizar** para atualização em massa a associação de contas AWS ligado a um grupo de gestão.
- Clique em **controlo de acesso** para definir a atribuição de função para o âmbito.

### <a name="aws-consolidated-account-permissions"></a>Permissões de conta AWS consolidados

Por predefinição, o AWS consolidados conta as permissões estão definidas durante a criação, com base nas permissões do conector de AWS. O criador de conector é o proprietário.

Gerir o nível de acesso usando a página de nível de acesso da conta AWS consolidados. No entanto, as permissões para o AWS consolidados conta não são herdadas por contas AWS ligadas.

## <a name="aws-linked-account"></a>Conta ligada do AWS

A conta AWS ligado é onde os recursos do AWS são criados e geridos. Uma conta ligada também atua como um limite de segurança.

A partir desta página, pode:

- Clique em **atualizar** para atualizar a associação de conta AWS ligado a um grupo de gestão.
- Clique em **controlo de acesso** para definir uma atribuição de função para o âmbito.

![Exemplo da página de conta ligada do AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>Permissões de conta AWS ligado

Por predefinição, as permissões de conta AWS ligado são definidas durante a criação, com base nas permissões do conector de AWS. O criador de conector é o proprietário. Gerir o nível de acesso usando a página de nível de acesso da conta AWS ligado. Permissões para a conta AWS consolidados não são herdadas por contas AWS ligadas.

Contas AWS ligadas sempre herdarem permissões do grupo de gestão que pertencem.

## <a name="next-steps"></a>Passos Seguintes

- Agora que configurar e configurado a integração de relatório de utilização e custos do AWS, avance para [utilização e os custos do AWS gerir](aws-integration-manage.md).
- Se não estiver familiarizado com a análise de custo, veja [explorar e analisar os custos com a análise de custo](quick-acm-cost-analysis.md) início rápido.
- Se não estiver familiarizado com orçamentos no Azure, veja [criar e gerir o Azure orçamentos](tutorial-acm-create-budgets.md) tutorial.
