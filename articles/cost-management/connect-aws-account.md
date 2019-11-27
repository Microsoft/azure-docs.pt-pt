---
title: Ligar uma conta do Amazon Web Services ao Cloudyn no Azure | Documentos da Microsoft
description: Ligar uma conta do Amazon Web Services para exibir dados de custo e a utilização em relatórios do Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 676c01a26d67b395340e5b1ed2dacc6b3b824742
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219745"
---
# <a name="connect-an-amazon-web-services-account"></a>Ligar uma conta do Amazon Web Services

Tem duas opções para ligar a sua conta Amazon Web Services (AWS) ao Cloudyn. Pode ligar-se com uma função IAM ou com uma conta de utilizador IAM só de leitura. A função IAM é recomendada porque pode delegar o acesso com permissões definidas para entidades fidedignas. A função IAM não precisa de partilhar chaves de acesso de longo prazo. Depois de ligar uma conta AWS ao Cloudyn, dados de utilização e custos estão disponíveis nos relatórios do Cloudyn. Este documento orienta por meio de ambas as opções.

Para obter mais informações sobre identidades IAM AWS, consulte [identidades (usuários, grupos e funções)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Além disso, ative o AWS detalhadas relatórios de faturação e armazenar as informações num bucket de serviço (S3) de armazenamento simples do AWS. Relatórios de faturas detalhados incluem custos de faturas com informações de marca e recursos numa base horária. Armazenar os relatórios permite o Cloudyn para recuperá-los a partir do seu registo e exibir as informações nos seus relatórios.


## <a name="aws-role-based-access"></a>Acesso baseado em funções do AWS

As seções a seguir explicam como criar uma função IAM só de leitura para fornecer acesso ao Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Obter o ID externo da conta Cloudyn

A primeira etapa é obter a frase de acesso de ligação exclusivo a partir do portal do Cloudyn. Ele é usado em AWS como a **ID externa**.

1. Abra o portal do Cloudyn no portal do Azure ou navegue até [https://azure.cloudyn.com](https://azure.cloudyn.com) e entre.
2. Clique no símbolo engrenagem e selecione **contas de nuvem**.
3. Em gerenciamento de contas, selecione a guia **contas do AWS** e clique em **Adicionar novo +** .
4. Na caixa de diálogo **adicionar conta do AWS** , copie a **ID externa** e salve o valor das etapas de criação de função do AWS na próxima seção. O ID externo é exclusivo para a sua conta. Na imagem a seguir, o exemplo de ID externa é _contoso_ seguido por um número. O ID é diferente.  
    ![ID externa mostrada na caixa Adicionar conta do AWS](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Adicionar AWS acesso só de leitura baseada em funções

1. Entre no console do AWS em https://console.aws.amazon.com/iam/home e selecione **funções**.
2. Clique em **criar função** e selecione **outra conta do AWS**.
3. Na caixa **ID da conta** , Cole `432263259397`. Este ID de conta é a conta de recoletor de dados do Cloudyn atribuída pelo AWS para o serviço do Cloudyn. Utilize o ID de conta exata apresentado.
4. Ao lado de **Opções**, selecione **exigir ID externa**. Cole o valor exclusivo que foi copiado anteriormente do campo **ID externa** em Cloudyn. Em seguida, clique em **Avançar: permissões**.  
    ![colar a ID externa de Cloudyn na página Criar função](./media/connect-aws-account/create-role01.png)
5. Em **anexar políticas de permissões**, na pesquisa da caixa de filtro **tipo de política** , digite `ReadOnlyAccess`, selecione **ReadOnlyAccess**e clique em **Avançar: examinar**.  
    ![selecione acesso somente leitura na lista de nomes de política](./media/connect-aws-account/readonlyaccess.png)
6. Na página revisão, verifique se suas seleções estão corretas e digite um **nome de função**. Por exemplo, *Azure-custo-gerenciamento*. Insira uma **Descrição de função**. Por exemplo, _atribuição de função para Cloudyn_e clique em **criar função**.
7. Na lista **funções** , clique na função que você criou e copie o valor da **função ARN** da página Resumo. Utilize o valor de função ARN (nome do recurso Amazon) mais tarde, quando se registra sua configuração no Cloudyn.  
    ![copiar a função ARN da página Resumo](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Configurar o acesso de função de AWS IAM no Cloudyn

1. Abra o portal do Cloudyn no portal do Azure ou navegue até https://azure.cloudyn.com/ e entre.
2. Clique no símbolo engrenagem e selecione **contas de nuvem**.
3. Em gerenciamento de contas, selecione a guia **contas do AWS** e clique em **Adicionar novo +** .
4. Em **nome da conta**, digite um nome para a conta.
5. Ao lado de **tipo de acesso**, selecione **função iam**.
6. No campo **função ARN** , Cole o valor copiado anteriormente e, em seguida, clique em **salvar**.  
    ![Cole a função ARN na caixa Adicionar conta AWS](./media/connect-aws-account/add-aws-account-box.png)


Sua conta AWS é apresentada na lista de contas. A **ID de proprietário** listada corresponde ao valor de ARN da função. O **status** da sua conta deve ter um símbolo de marca de seleção verde indicando que o Cloudyn pode acessar sua conta do AWS. Até que você habilite a cobrança detalhada do AWS, o status de consolidação aparece como **autônomo**.

![Estado da conta AWS mostrado na página Gestão de contas](./media/connect-aws-account/aws-account-status01.png)

Começa a Cloudyn recolher os dados e preencher relatórios. Em seguida, [habilite a cobrança detalhada do AWS](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Acesso com base no utilizador do AWS

As seções a seguir explicam como criar um utilizador só de leitura para fornecer acesso ao Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Adicionar AWS acesso só de leitura com base no utilizador

1. Entre no console do AWS em https://console.aws.amazon.com/iam/home e selecione **usuários**.
2. Clique em **Adicionar usuário**.
3. No campo **nome de usuário** , digite um nome de usuário.
4. Para **tipo de acesso**, selecione **acesso programático** e clique em **Avançar: permissões**.  
    ![Insira um nome de usuário na página Adicionar usuário](./media/connect-aws-account/add-user01.png)
5. Para permissões, selecione **anexar políticas existentes diretamente**.
6. Em **anexar políticas de permissões**, na pesquisa da caixa de filtro **tipo de política** , digite `ReadOnlyAccess`, selecione **ReadOnlyAccess**e clique em **Avançar: revisão**.  
    ![selecione ReadOnlyAccess para definir permissões para o usuário](./media/connect-aws-account/set-permission-for-user.png)
7. Na página revisão, verifique se suas seleções estão corretas e clique em **criar usuário**.
8. Na página conclua, a sua chave ID e segredo de acesso à chave de acesso são apresentados. Utilize estas informações para configurar o registo no Cloudyn.
9. Clique em **baixar. csv** e salve o arquivo Credentials. csv em um local seguro.  
    ![clique em baixar. csv para salvar as credenciais](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Configurar o acesso de utilizador com base de AWS IAM no Cloudyn

1. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com/ e inicie sessão.
2. Clique no símbolo engrenagem e selecione **contas de nuvem**.
3. Em gerenciamento de contas, selecione a guia **contas do AWS** e clique em **Adicionar novo +** .
4. Para **nome da conta**, digite um nome de conta.
5. Ao lado de **tipo de acesso**, selecione **usuário iam**.
6. Em **chave de acesso**, Cole o valor da **ID de chave de acesso** do arquivo Credentials. csv.
7. Em **chave secreta**, Cole o valor da **chave de acesso secreto** do arquivo Credentials. csv e, em seguida, clique em **salvar**.  

Sua conta AWS é apresentada na lista de contas. O **status** da sua conta deve ter um símbolo de marca de seleção verde.

Começa a Cloudyn recolher os dados e preencher relatórios. Em seguida, [habilite a cobrança detalhada do AWS](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Ativar a faturação do AWS detalhada

Utilize os seguintes passos para obter sua ARN de função do AWS. Utilize a função ARN para conceder as permissões de leitura para um bucket de faturação.

1. Entre no console do AWS em https://console.aws.amazon.com e selecione **Serviços**.
2. Na caixa de pesquisa de serviço, digite *iam*e selecione essa opção.
3. Selecione **funções** no menu à esquerda.
4. Na lista de funções, selecione a função que criou para o acesso ao Cloudyn.
5. Na página Resumo de funções, clique para copiar a **função ARN**. Mantenha o ARN função útil para os passos seguintes.

### <a name="create-an-s3-bucket"></a>Criar um registo de S3

Criar um registo de S3 para armazenar informações detalhadas de faturas.

1. Entre no console do AWS em https://console.aws.amazon.com e selecione **Serviços**.
2. Na caixa de pesquisa de serviço, digite *S3*e selecione **S3**.
3. Na página Amazon S3, clique em **criar Bucket**.
4. No Assistente para criar Bucket, escolha um nome de Bucket e uma região e clique em **Avançar**.  
    ![informações de exemplo, uma página Criar Bucket](./media/connect-aws-account/create-bucket.png)
5. Na página **definir propriedades** , mantenha os valores padrão e clique em **Avançar**.
6. Na página revisão, clique em **criar Bucket**. É apresentada a lista de registo.
7. Clique no Bucket que você criou e selecione a guia **permissões** e, em seguida, selecione **política de Bucket**. É aberto o editor de política de registo.
8. Copie o seguinte exemplo JSON e cole-o no editor de políticas de registo.
   - Substitua `<BillingBucketName>` pelo nome do Bucket S3.
   - Substitua `<ReadOnlyUserOrRole>` pela função ou ARN de usuário que você copiou anteriormente.

   ```json
   {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
   }
   ```

9. Clique em **Guardar**.  
    ![clique em salvar no editor de política de Bucket](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Ativar os relatórios de faturação do AWS

Depois de criar e configurar o Bucket S3, navegue até as [preferências de cobrança](https://console.aws.amazon.com/billing/home?#/preference) no console do AWS.

1. Na página preferências, selecione **receber relatórios de cobrança**.
2. Em **receber relatórios de cobrança**, insira o nome do Bucket que você criou e, em seguida, clique em **verificar**.  
3. Selecione todas as quatro opções de granularidade de relatório e clique em **salvar preferências**.  
    ![selecionar granularidade para habilitar relatórios](./media/connect-aws-account/enable-reports.png)

Cloudyn obtém informações detalhadas de faturas do seu registo de S3 e preenche relatórios depois detalhada a faturação é ativada. Pode demorar até 24 horas até que os dados de faturação detalhados surge na consola do Cloudyn. Quando os dados de cobrança detalhados estiverem disponíveis, o status de consolidação da conta será exibido como **consolidado**. O status da conta aparece como **concluído**.

![Estado de consolidação apresentado no separador contas AWS](./media/connect-aws-account/consolidated-status.png)

Alguns dos relatórios de otimização podem requerer alguns dias de dados e ter um tamanho de amostra de dados adequado para obter recomendações precisas.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o Cloudyn, continue no tutorial [analisar o uso e os custos](tutorial-review-usage.md) do Cloudyn.
