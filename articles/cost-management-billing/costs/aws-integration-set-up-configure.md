---
title: Configurar a integração do AWS com o gerenciamento de custos do Azure
description: Este artigo explica como configurar e configurar a integração de relatório de uso e custo do AWS com o gerenciamento de custos do Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 5dbc6ca836c8d1c8b717fd4bf23eab5aa360a288
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988698"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Configurar e configurar a integração de relatório de uso e custo do AWS

Com a integração de Amazon Web Services (AWS) de custo e uso (CUR), você monitora e controla seus gastos de AWS no gerenciamento de custos do Azure. A integração permite um único local no portal do Azure onde você monitora e controla os gastos para o Azure e o AWS. Este artigo explica como configurar a integração e configurá-la para que você possa usar os recursos de gerenciamento de custos do Azure para analisar os custos e revisar os orçamentos.

O gerenciamento de custos processa o relatório de custos e uso do AWS armazenado em um Bucket S3 usando suas credenciais de acesso do AWS para obter definições de relatório e baixar arquivos CSV de arquivo.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Criar um relatório de custo e uso no AWS

Usar um relatório de custo e uso é a maneira AWS recomendada de coletar e processar os custos de AWS. Para obter mais informações, consulte a documentação de [relatório de uso e custo do AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) .

Use a página **relatórios de uso & de custo** do console de gerenciamento de cobrança e custo no AWS para criar um relatório de custo e uso com as seguintes etapas:

1. Entre no console de gerenciamento do AWS e abra o [console de gerenciamento de cobrança e custo](https://console.aws.amazon.com/billing).
2. No painel de navegação, selecione **custos & relatórios de uso**.
3. Selecione **criar relatório**.
4. Para **nome do relatório**, insira um nome para o relatório.
5. Em **detalhes do relatório adicional**, selecione **incluir IDs de recurso**.
6. Para **configurações de atualização de dados**, selecione se deseja que o relatório de custo e uso do AWS seja atualizado se AWS aplicar reembolsos, créditos ou taxas de suporte à sua conta depois de finalizar sua fatura. Quando um relatório é atualizado, um novo relatório é carregado no Amazon S3. Recomendamos que você deixe a configuração selecionada.
7. Selecione **Seguinte**.
8. Para o **Bucket S3**, escolha **Configurar**.
9. Na caixa de diálogo Configurar Bucket S3, execute uma das seguintes tarefas:
    1. Selecione um Bucket existente na lista suspensa e escolha **Avançar**.
    2. Insira um nome de Bucket e a região em que você deseja criar um novo Bucket e escolha **Avançar**.
10. Selecione **Eu confirmei que essa política está correta**e, em seguida, clique em **salvar**.
11. Adicional Para o prefixo do caminho do relatório, insira o prefixo do caminho do relatório que você deseja que seja anexado ao nome do relatório.
Se você não especificar um prefixo, o prefixo padrão será o nome que você especificou para o relatório. O intervalo de datas tem o formato `/report-name/date-range/`.
12. Para **unidade de tempo**, escolha por **hora**.
13. Para **controle de versão de relatório**, escolha se você deseja que cada versão do relatório substitua a versão anterior ou se deseja novos relatórios adicionais.
14. Para **habilitar a integração de dados para**o, nenhuma seleção é necessária.
15. Para **compactação**, selecione **gzip**.
16. Selecione **Seguinte**.
17. Depois de examinar as configurações do relatório, selecione **revisar e concluir**.

    Observe o nome do relatório. Você o usará em etapas posteriores.

Pode levar até 24 horas para o AWS começar a entregar relatórios ao seu Bucket S3 do Amazon. Após o início da entrega, o AWS atualiza os arquivos de relatório de custo e uso do AWS pelo menos uma vez por dia. Você pode continuar configurando seu ambiente AWS sem esperar que a entrega seja iniciada.

## <a name="create-a-role-and-policy-in-aws"></a>Criar uma função e uma política no AWS

O gerenciamento de custos do Azure acessa o Bucket S3 em que o relatório de custo e uso está localizado várias vezes por dia. O serviço precisa acessar as credenciais para verificar se há novos dados. Você cria uma função e uma política no AWS para permitir que o gerenciamento de custos o acesse.

Para habilitar o acesso baseado em função a uma conta do AWS no gerenciamento de custos, a função é criada no console do AWS. Você precisa ter a _função ARN_ e a _ID externa_ do console do AWS. Posteriormente, use-os na página **criar um conector do AWS** no gerenciamento de custos.

Use o assistente para criação de uma nova função:

1. Entre no console do AWS e selecione **Serviços**.
2. Na lista de serviços, selecione **iam**.
3. Selecione **funções** e, em seguida, selecione **criar função**.
4. Na página seguinte, selecione **outra conta do AWS**.
5. Em **ID da conta**, insira **432263259397**.
6. Em **Opções**, selecione **exigir ID externa (prática recomendada quando uma terceira parte assumirá essa função)** .
7. Em **ID externa**, insira a ID externa, que é uma senha compartilhada entre a função AWS e o gerenciamento de custos do Azure. A mesma ID externa também é usada na nova página de **conector** no gerenciamento de custos. A Microsoft recomenda que você use uma política de senha forte ao inserir a ID externa.

    > [!NOTE]
    > Não altere a seleção para **exigir MFA**. Ele deve permanecer limpo.
8. Selecione **Avançar: permissões**.
9. Selecione **criar política**. É aberto um novo separador do browser. É aí que você cria uma política.
10. Selecione **escolher um serviço**.

Configure a permissão para o relatório de custo e uso:

1. Insira o **relatório de custo e uso**.
2. Selecione **nível de acesso** > **ler** > **DescribeReportDefinitions**. Esta etapa permite que o gerenciamento de custos Leia o que os relatórios de CUR são definidos e determine se eles correspondem ao pré-requisito de definição de relatório.
3. Selecione **adicionar permissões adicionais**.

Configure a permissão para seu Bucket e objetos S3:

1. Selecione **escolher um serviço**.
2. Insira **S3**.
3. Selecione a **lista** de > de **nível de acesso** > **ListBucket**. Esta ação Obtém a lista de objetos no Bucket S3.
4. Selecione **nível de acesso** > **ler** > **GetObject**. Essa ação permite o download de arquivos de cobrança.
5. Selecione **recursos**.
6. Selecione **Bucket – adicionar ARN**.
7. Em **nome do Bucket**, insira o Bucket usado para armazenar os arquivos da cur.
8. Selecione **objeto – Adicionar ARN**.
9. Em **nome do Bucket**, insira o Bucket usado para armazenar os arquivos da cur.
10. Em **nome do objeto**, selecione **qualquer**.
11. Selecione **adicionar permissões adicionais**.

Configurar permissão para o Gerenciador de custo:

1. Selecione **escolher um serviço**.
2. Insira o **serviço Gerenciador de custo**.
3. Selecione **todas as ações de serviço do Gerenciador de custos (CE:\*)** . Esta ação valida que a coleção está correta.
4. Selecione **adicionar permissões adicionais**.

Adicionar permissão para organizações AWS:

1. Insira **organizações**.
2. Selecione a **lista** de > de **nível de acesso** > **ListAccounts**. Essa ação Obtém os nomes das contas.
3. Em **política de revisão**, insira um nome para a nova política. Verifique se você inseriu as informações corretas e, em seguida, selecione **criar política**.
4. Volte para a guia anterior e atualize a página da Web do navegador. Na barra de pesquisa, procure sua nova política.
5. Selecione **Avançar: examinar**.
6. Insira um nome para a nova função. Verifique se você inseriu as informações corretas e, em seguida, selecione **criar função**.

    Observe a função ARN e a ID externa usada nas etapas anteriores quando você criou a função. Você os usará posteriormente quando configurar o conector de gerenciamento de custos do Azure.

A política JSON deve ser semelhante ao exemplo a seguir. Substitua _bucketname_ pelo nome do Bucket S3.

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

## <a name="set-up-a-new-aws-connector-in-azure"></a>Configurar um novo conector do AWS no Azure

Use as informações a seguir para criar um conector do AWS e começar a monitorar seus custos de AWS:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Vá para **Gerenciamento de custos + cobrança** > **Gerenciamento de custos**.
3. Em **configurações**, selecione **conectores de nuvem (versão prévia)** .  
    ![exemplo mostrando a configuração de conectores de nuvem (versão prévia)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Selecione **+ Adicionar** na parte superior da página para criar um conector.
5. Na página **criar um conector do AWS** , em **nome de exibição**, insira um nome para o conector.  
    ![exemplo da página para criar um conector do AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Opcionalmente, selecione o grupo de gerenciamento padrão. Ele armazenará todas as contas vinculadas descobertas. Você pode configurá-lo mais tarde.
7. Na seção **cobrança** , selecione **cobrar automaticamente 1% em disponibilidade geral** se você quiser garantir a operação contínua quando a visualização expirar. Se você selecionar a opção automática, deverá selecionar uma assinatura de cobrança.
8. Para a **função ARN**, insira o valor que você usou ao configurar a função em AWS.
9. Para **ID externa**, insira o valor que você usou ao configurar a função em AWS.
10. Para **nome do relatório**, insira o nome que você criou em AWS.
11. Selecione **Avançar** e, em seguida, selecionar **criar**.

Pode levar algumas horas para os novos escopos AWS, conta consolidada AWS, contas vinculadas do AWS e seus dados de custo para serem exibidos.

Depois de criar o conector, recomendamos que você atribua o controle de acesso a ele. Os usuários recebem permissões para os escopos recém-descobertos: conta consolidada AWS e contas vinculadas AWS. O usuário que cria o conector é o proprietário do conector, a conta consolidada e todas as contas vinculadas.

A atribuição de permissões de conector a usuários após a descoberta ocorre não atribui permissões aos escopos de AWS existentes. Em vez disso, somente as novas contas vinculadas são atribuídas permissões.

## <a name="take-additional-steps"></a>Executar etapas adicionais

- [Configure grupos de gerenciamento](../../governance/management-groups/overview.md#initial-setup-of-management-groups), se ainda não tiver feito isso.
- Verifique se novos escopos são adicionados ao seu seletor de escopo. Selecione **Atualizar** para exibir os dados mais recentes.
- Na página **conectores de nuvem** , selecione seu conector e selecione **ir para conta de cobrança** para atribuir a conta vinculada aos grupos de gerenciamento.

## <a name="manage-cloud-connectors"></a>Gerenciar conectores de nuvem

Ao selecionar um conector na página **conectores de nuvem** , você pode:

- Selecione **ir para conta de cobrança** para exibir informações para a conta consolidada do AWS.
- Selecione **controle de acesso** para gerenciar a atribuição de função para o conector.
- Selecione **Editar** para atualizar o conector. Não é possível alterar o número da conta AWS, pois ele aparece na função ARN. Mas você pode criar um novo conector.
- Selecione **verificar** para executar novamente o teste de verificação para garantir que o gerenciamento de custos possa coletar dados usando as configurações do conector.

![Exemplo de lista de conectores AWS criados](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Configurar grupos de gerenciamento do Azure

Coloque suas assinaturas do Azure e as contas vinculadas do AWS no mesmo grupo de gerenciamento para criar um único local onde você possa ver as informações do provedor entre nuvens. Se você ainda não tiver configurado seu ambiente do Azure com grupos de gerenciamento, consulte [configuração inicial dos grupos de gerenciamento](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Se você quiser separar os custos, poderá criar um grupo de gerenciamento que contenha apenas contas vinculadas do AWS.

## <a name="set-up-an-aws-consolidated-account"></a>Configurar uma conta consolidada do AWS

A conta consolidada do AWS combina cobrança e pagamento para várias contas do AWS. Ele também atua como uma conta vinculada do AWS.

![Detalhes de exemplo para uma conta consolidada AWS](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Na página, você pode:

- Selecione **Atualizar** para atualizar em massa a associação de contas VINCULAdas AWS a um grupo de gerenciamento.
- Selecione **controle de acesso** para definir a atribuição de função para o escopo.

### <a name="permissions-for-an-aws-consolidated-account"></a>Permissões para uma conta consolidada do AWS

Por padrão, as permissões para uma conta consolidada AWS são definidas na criação da conta, com base nas permissões do conector AWS. O criador do conector é o proprietário.

Você gerencia o nível de acesso usando a página **nível de acesso** da conta consolidada do AWS. No entanto, as contas vinculadas AWS não herdam permissões para a conta consolidada do AWS.

## <a name="set-up-an-aws-linked-account"></a>Configurar uma conta vinculada do AWS

A conta vinculada do AWS é onde os recursos do AWS são criados e gerenciados. Uma conta vinculada também age como um limite de segurança.

Nessa página, você pode:

- Selecione **Atualizar** para atualizar a associação de uma conta vinculada do AWS com um grupo de gerenciamento.
- Selecione **controle de acesso** para definir uma atribuição de função para o escopo.

![Exemplo da página da conta vinculada do AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Permissões para uma conta vinculada do AWS

Por padrão, as permissões para uma conta vinculada AWS são definidas na criação, com base nas permissões do conector AWS. O criador do conector é o proprietário. Você gerencia o nível de acesso usando a página **nível de acesso** da conta vinculada do AWS. As contas vinculadas do AWS não herdam as permissões de uma conta consolidada do AWS.

As contas vinculadas do AWS sempre herdam as permissões do grupo de gerenciamento ao qual pertencem.

## <a name="next-steps"></a>Passos seguintes

- Agora que você configurou e configurou a integração de relatório de uso e custo do AWS, continue a [gerenciar os custos e o uso do AWS](aws-integration-manage.md).
- Se você não estiver familiarizado com a análise de custo, consulte [explorar e analisar custos com](quick-acm-cost-analysis.md) o guia de início rápido de análise de custo.
- Se você não estiver familiarizado com os orçamentos no Azure, consulte [criar e gerenciar orçamentos do Azure](tutorial-acm-create-budgets.md).
