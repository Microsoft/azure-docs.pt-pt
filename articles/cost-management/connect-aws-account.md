---
title: Ligar uma conta do Amazon Web Services ao Cloudyn no Azure | Documentos da Microsoft
description: Ligar uma conta do Amazon Web Services para exibir dados de custo e a utilização em relatórios do Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 44bf1d9cd270394720aee71862c1e65118084259
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978234"
---
# <a name="connect-an-amazon-web-services-account"></a>Ligar uma conta do Amazon Web Services

Tem duas opções para ligar a sua conta Amazon Web Services (AWS) ao Cloudyn. Pode ligar-se com uma função IAM ou com uma conta de utilizador IAM só de leitura. A função IAM é recomendada porque pode delegar o acesso com permissões definidas para entidades fidedignas. A função IAM não precisa de partilhar chaves de acesso de longo prazo. Depois de ligar uma conta AWS ao Cloudyn, dados de utilização e custos estão disponíveis nos relatórios do Cloudyn. Este documento orienta por meio de ambas as opções.

Para obter mais informações sobre as identidades de IAM do AWS, consulte [identidades (utilizadores, grupos e funções)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Além disso, ative o AWS detalhadas relatórios de faturação e armazenar as informações num bucket de serviço (S3) de armazenamento simples do AWS. Relatórios de faturas detalhados incluem custos de faturas com informações de marca e recursos numa base horária. Armazenar os relatórios permite o Cloudyn para recuperá-los a partir do seu registo e exibir as informações nos seus relatórios.


## <a name="aws-role-based-access"></a>Acesso baseado em funções do AWS

As seções a seguir explicam como criar uma função IAM só de leitura para fornecer acesso ao Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Obter o ID externo da conta Cloudyn

A primeira etapa é obter a frase de acesso de ligação exclusivo a partir do portal do Cloudyn. Ele é usado no AWS como a **ID externo**.

1. Abrir o portal da Cloudyn a partir do portal do Azure ou navegue até [ https://azure.cloudyn.com ](https://azure.cloudyn.com) e iniciar sessão.
2. Clique no símbolo de engrenagem e, em seguida, selecione **contas da Cloud**.
3. Na gestão de contas, selecione o **contas AWS** separador e, em seguida, clique em **adicionar novo +**.
4. Na **adicionar conta AWS** caixa de diálogo, copie a **ID externo** e guarde o valor para a função de AWS criação os passos na secção seguinte. O ID externo é exclusivo para a sua conta. ID externo na imagem seguinte, o exemplo é _Contoso_ seguido de um número. O ID é diferente.  
    ![ID externo](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Adicionar AWS acesso só de leitura baseada em funções

1. Inicie sessão na consola AWS https://console.aws.amazon.com/iam/home e selecione **funções**.
2. Clique em **criar função** e, em seguida, selecione **conta AWS outro**.
3. Na **ID da conta** caixa, cole `432263259397`. Este ID de conta é a conta de recoletor de dados do Cloudyn atribuída pelo AWS para o serviço do Cloudyn. Utilize o ID de conta exata apresentado.
4. Junto a **opções**, selecione **necessitam de ID externo**. Cole o valor exclusivo que anteriormente copiados a partir da **ID externo** campo no Cloudyn. Em seguida, clique em **seguinte: permissões**.  
    ![Criar função](./media/connect-aws-account/create-role01.png)
5. Em **anexar as políticas de permissões**, na **tipo de política** pesquisa da caixa de filtro, escreva `ReadOnlyAccess`, selecione **ReadOnlyAccess**, em seguida, clique em **seguinte: Revisão**.  
    ![Acesso só de leitura](./media/connect-aws-account/readonlyaccess.png)
6. Na página revisão, certifique-se as suas seleções estão corretas e escrevem um **nome da função**. Por exemplo, *custo-Azure-gestão*. Introduza um **descrição da função**. Por exemplo, _atribuição de função para o Cloudyn_, em seguida, clique em **criar função**.
7. Na **funções** lista, clique na função que criou e copie a **função ARN** valor da página de resumida. Utilize o valor de função ARN (nome do recurso Amazon) mais tarde, quando se registra sua configuração no Cloudyn.  
    ![Função ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Configurar o acesso de função de AWS IAM no Cloudyn

1. Abrir o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com/ e iniciar sessão.
2. Clique no símbolo de engrenagem e, em seguida, selecione **contas da Cloud**.
3. Na gestão de contas, selecione o **contas AWS** separador e, em seguida, clique em **adicionar novo +**.
4. Na **nome da conta**, escreva um nome para a conta.
5. Junto a **tipo de acesso**, selecione **IAM função**.
6. Na **função ARN** campo, cole o valor que copiou anteriormente e, em seguida, clique em **guardar**.  
    ![Adicionar caixa conta AWS](./media/connect-aws-account/add-aws-account-box.png)


Sua conta AWS é apresentada na lista de contas. O **ID do proprietário** listados corresponde ao seu valor ARN de função. Sua **Status da conta** deve ter um símbolo de marca de verificação verde a indicar que o Cloudyn pode acessar sua conta AWS. Até ativar a faturação de AWS detalhada, seu estado de consolidação é apresentado como **autónomo**.

![Estado da conta AWS](./media/connect-aws-account/aws-account-status01.png)

Começa a Cloudyn recolher os dados e preencher relatórios. Em seguida, [ativar a faturação do AWS detalhada](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Acesso com base no utilizador do AWS

As seções a seguir explicam como criar um utilizador só de leitura para fornecer acesso ao Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Adicionar AWS acesso só de leitura com base no utilizador

1. Inicie sessão na consola AWS https://console.aws.amazon.com/iam/home e selecione **utilizadores**.
2. Clique em **adicionar utilizador**.
3. Na **nome de utilizador** , digite um nome de utilizador.
4. Para **tipo de acesso**, selecione **acesso programático** e clique em **seguinte: permissões**.  
    ![Adicionar utilizador](./media/connect-aws-account/add-user01.png)
5. Para permissões, selecione **anexar as políticas existentes diretamente**.
6. Sob **políticas de permissões de anexar**, na **tipo de política** pesquisa da caixa de filtro, escreva `ReadOnlyAccess`, selecione **ReadOnlyAccess**e, em seguida, clique em **seguinte : Reveja**.  
    ![Conjunto de permissões de utilizador](./media/connect-aws-account/set-permission-for-user.png)
7. Na página revisão, certifique-se as suas seleções estão corretas, em seguida, clique em **criar utilizador**.
8. Na página conclua, a sua chave ID e segredo de acesso à chave de acesso são apresentados. Utilize estas informações para configurar o registo no Cloudyn.
9. Clique em **transferir. csv** e guarde o ficheiro de credentials.csv para uma localização segura.  
    ![Transferir as credenciais](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Configurar o acesso de utilizador com base de AWS IAM no Cloudyn

1. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com/ e inicie sessão.
2. Clique no símbolo de engrenagem e, em seguida, selecione **contas da Cloud**.
3. Na gestão de contas, selecione o **contas AWS** separador e, em seguida, clique em **adicionar novo +**.
4. Para **nome da conta**, escreva um nome de conta.
5. Junto a **tipo de acesso**, selecione **IAM utilizador**.
6. Na **chave de acesso**, cole a **acesso ID da chave** valor a partir do ficheiro credentials.csv.
7. Na **chave secreta**, cole a **chave de acesso secreta** valor a partir do ficheiro credentials.csv e, em seguida, clique em **guardar**.  

Sua conta AWS é apresentada na lista de contas. Sua **Status da conta** deve ter um símbolo de marca de verificação verde.

Começa a Cloudyn recolher os dados e preencher relatórios. Em seguida, [ativar a faturação do AWS detalhada](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Ativar a faturação do AWS detalhada

Utilize os seguintes passos para obter sua ARN de função do AWS. Utilize a função ARN para conceder as permissões de leitura para um bucket de faturação.

1. Inicie sessão na consola AWS https://console.aws.amazon.com e selecione **serviços**.
2. Na pesquisa de serviço de tipo caixa *IAM*e selecione essa opção.
3. Selecione **funções** no menu esquerdo.
4. Na lista de funções, selecione a função que criou para o acesso ao Cloudyn.
5. Na página de resumo de funções, clique para copiar o **função ARN**. Mantenha o ARN função útil para os passos seguintes.

### <a name="create-an-s3-bucket"></a>Criar um registo de S3

Criar um registo de S3 para armazenar informações detalhadas de faturas.

1. Inicie sessão na consola AWS https://console.aws.amazon.com e selecione **serviços**.
2. Na pesquisa de serviço de tipo caixa *S3*e selecione **S3**.
3. Na página do Amazon S3, clique em **bucket de criar**.
4. No Assistente de registo de criar, escolha um nome de registo e a região e, em seguida, clique em **seguinte**.  
    ![Criar registo](./media/connect-aws-account/create-bucket.png)
5. Sobre o **definir as propriedades** página, mantenha os valores predefinidos e, em seguida, clique em **próxima**.
6. Na página revisão, clique em **bucket de criar**. É apresentada a lista de registo.
7. Clique com o registo que criou e selecione o **permissões** separador e, em seguida, selecione **política de Bucket**. É aberto o editor de política de registo.
8. Copie o seguinte exemplo JSON e cole-o no editor de políticas de registo.
  - Substitua `<BillingBucketName>` com o nome do seu registo de S3.
  - Substitua `<ReadOnlyUserOrRole>` com a função ou utilizador ARN que tinha anteriormente copiou.

  ```
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
    ![Editor de política de registo](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Ativar os relatórios de faturação do AWS

Depois de criar e configurar o registo de S3, navegue até [faturação preferências](https://console.aws.amazon.com/billing/home?#/preference) na consola do AWS.

1. Na página de preferências, selecione **receber relatórios de faturação**.
2. Sob **receber relatórios de faturação**, introduza o nome do registo de que criou e, em seguida, clique em **verificar**.  
3. Selecione todas as quatro opções de granularidade de relatório e, em seguida, clique em **salvar preferências**.  
    ![Ativar relatórios](./media/connect-aws-account/enable-reports.png)

Cloudyn obtém informações detalhadas de faturas do seu registo de S3 e preenche relatórios depois detalhada a faturação é ativada. Pode demorar até 24 horas até que os dados de faturação detalhados surge na consola do Cloudyn. Quando os dados de faturação detalhados estão disponíveis, do Estado de consolidação da conta é apresentado como **consolidado**. Estado da conta é apresentado como **concluído**.

![Estado consolidado da conta](./media/connect-aws-account/consolidated-status.png)

Alguns dos relatórios de otimização podem requerer alguns dias de dados e ter um tamanho de amostra de dados adequado para obter recomendações precisas.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre o Cloudyn, avance para o [rever a utilização e custos](tutorial-review-usage.md) tutorial da Cloudyn.
