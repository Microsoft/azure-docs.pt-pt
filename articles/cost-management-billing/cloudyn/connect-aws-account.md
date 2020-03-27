---
title: Ligar uma conta do Amazon Web Services à Cloudyn no Azure
description: Ligue uma conta do Amazon Web Services para ver os custos e os dados de utilização nos relatórios da Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 38e5e253c32a2f85e18c80bdefa7d3b640da2e50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79464447"
---
# <a name="connect-an-amazon-web-services-account"></a>Ligar uma conta do Amazon Web Services

Tem duas opções para ligar a sua conta do Amazon Web Services (AWS) à Cloudyn. Pode ligar com uma função IAM ou uma conta de utilizador IAM só de leitura. A função IAM é recomendada porque permite delegar o acesso com permissões definidas a entidades fidedignas. A função IAM não requer a partilha de chaves de acesso a longo prazo. Após ligar uma conta do AWS à Cloudyn, os custos e os dados de utilização ficam disponíveis nos relatórios da Cloudyn. Este documento guia-o através de ambas as opções.

Para obter mais informações sobre as identidades IAM do AWS, veja [Identidades (Utilizadores, Grupos e Funções)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Além disso, ativa relatórios de faturação detalhados do AWS e armazena as informações num registo do Simple Storage Service (S3) do AWS. Os relatórios de faturação detalhados incluem custos de faturação com informações de etiquetas e recursos por hora. Armazenar os relatórios permite à Cloudyn obtê-los a partir do registo e mostrar as informações nos relatórios.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]
## <a name="aws-role-based-access"></a>Acesso baseado em funções do AWS

As secções seguintes orientam-no pela criação de uma função IAM só de leitura para fornecer acesso à Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Obter o ID externo da conta da Cloudyn

O primeiro passo é obter a frase de acesso de ligação exclusiva no portal da Cloudyn. É utilizada no AWS como **ID Externo**.

1. Abra o portal da Cloudyn no portal do Azure ou navegue para [https://azure.cloudyn.com](https://azure.cloudyn.com) e inicie sessão.
2. Clique no símbolo de engrenagem e, em seguida, selecione **Contas da Cloud**.
3. Em Gestão de Contas, selecione o separador **Contas do AWS** e clique em **Adicionar nova +** .
4. Na caixa de diálogo **Adicionar Conta do AWS**, copie o **ID Externo** e guarde o valor para os passos de criação da Função do AWS na secção seguinte. O ID Externo é exclusivo da sua conta. Na imagem seguinte, o ID Externo de exemplo é _Contoso_ seguido de um número. O seu ID é diferente.  
    ![ID externo apresentado na caixa Adicionar Conta AWS](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Adicionar acesso baseado em funções só de leitura do AWS

1. Inicie sessão na consola do AWS em [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) e selecione **Funções**.
2. Clique em **Criar Função** e, em seguida, selecione **Outra conta do AWS**.
3. Na caixa **ID da Conta**, cole `432263259397`. Este ID da Conta é a conta do recoletor de dados da Cloudyn atribuída pelo AWS ao serviço da Cloudyn. Utilize o ID da Conta exato apresentado.
4. Junto a **Opções**, selecione **Exigir ID externo**. Cole o valor exclusivo que copiou anteriormente a partir do campo **ID Externo** na Cloudyn. Depois, clique em **Seguinte: Permissões**.  
    ![colar o ID Externo da Cloudyn na página Criar função](./media/connect-aws-account/create-role01.png)
5. Em **Anexar políticas de permissões**, na pesquisa da caixa de filtro **Tipo de política**, escreva `ReadOnlyAccess`, selecione **ReadOnlyAccess** e clique em **Seguinte: Revisão**.  
    ![selecionar Acesso só de leitura na lista de nomes de políticas](./media/connect-aws-account/readonlyaccess.png)
6. Na página Rever, verifique se as suas seleções estão corretas e escreva um **Nome de função**. Por exemplo, *Azure-Cost-Mgt*. Introduza a **Descrição da função**. Por exemplo, _Atribuição de funções para a Cloudyn_ e, em seguida, clique em **Criar função**.
7. Na lista **Funções**, clique na função que criou e copie o valor **ARN da Função** na página Resumo. Utilize o valor ARN da Função (Amazon Resource Name) posteriormente quando registar a sua configuração na Cloudyn.  
    ![copiar o ARN da Função na página Resumo](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Configurar o acesso a funções IAM do AWS na Cloudyn

1. Abra o portal da Cloudyn no portal do Azure ou navegue para https://azure.cloudyn.com/ e inicie sessão.
2. Clique no símbolo de engrenagem e, em seguida, selecione **Contas da Cloud**.
3. Em Gestão de Contas, selecione o separador **Contas do AWS** e clique em **Adicionar nova +** .
4. Em **Nome da Conta**, escreva um nome para a conta.
5. Junto a **Tipo de Acesso**, selecione **Função IAM**.
6. No campo **ARN da Função**, cole o valor que copiou anteriormente e, em seguida, clique em **Guardar**.  
    ![colar o ARN da Função na caixa Adicionar Conta do AWS](./media/connect-aws-account/add-aws-account-box.png)


A sua conta do AWS aparece na lista de contas. O **ID do Proprietário** listado corresponde ao valor do ARN da Função. O **Estado da Conta** deve ter um símbolo de marca de verificação verde a indicar que a Cloudyn consegue aceder à sua conta do AWS. Até ativar a faturação detalhada do AWS, o estado de consolidação aparece como **Autónomo**.

![Estado da conta do AWS apresentado na página Gestão de Contas](./media/connect-aws-account/aws-account-status01.png)

A Cloudyn começa a recolher os dados e a povoar os relatórios. Em seguida, [ative a faturação detalhada do AWS](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Acesso baseado no utilizador do AWS

As secções seguintes orientam-no pela criação de um utilizador só de leitura para fornecer acesso à Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Adicionar acesso baseado no utilizador só de leitura do AWS

1. Inicie sessão na consola do AWS em [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) e selecione **Utilizadores**.
2. Clique em **Adicionar Utilizador**.
3. No campo **Nome de utilizador**, escreva um nome de utilizador.
4. Para **Tipo de acesso**, selecione **Acesso programático** e clique em **Seguinte: Permissões**.  
    ![introduzir um nome de utilizador na página Adicionar utilizador](./media/connect-aws-account/add-user01.png)
5. Para permissões, selecione **Anexar políticas existentes diretamente**.
6. Em **Anexar políticas de permissões**, na pesquisa da caixa de filtro **Tipo de política**, escreva `ReadOnlyAccess`, selecione **ReadOnlyAccess** e clique em **Seguinte: Revisão**.  
    ![selecionar ReadOnlyAccess para definir as permissões do utilizador](./media/connect-aws-account/set-permission-for-user.png)
7. Na página Rever, verifique se as suas seleções estão corretas e clique em **Criar utilizador**.
8. Na página Concluir, são apresentados o ID da chave de acesso e a Chave de acesso secreta. Pode utilizar estas informações para configurar o registo na Cloudyn.
9. Clique em **Transferir .csv** e guarde o ficheiro credentials.csv numa localização segura.  
    ![clicar em Transferir .csv para guardar as credenciais](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Configurar o acesso baseado no utilizador IAM do AWS na Cloudyn

1. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com/ e inicie sessão.
2. Clique no símbolo de engrenagem e, em seguida, selecione **Contas da Cloud**.
3. Em Gestão de Contas, selecione o separador **Contas do AWS** e clique em **Adicionar nova +** .
4. Para **Nome da Conta**, escreva um nome de conta.
5. Junto a **Tipo de Acesso**, selecione **Utilizador IAM**.
6. Em **Chave de Acesso**, cole o valor do **ID da chave de acesso** do ficheiro credentials.csv.
7. Em **Chave Secreta**, cole o valor da **Chave de acesso secreta** do ficheiro credentials.csv e clique em **Guardar**.  

A sua conta do AWS aparece na lista de contas. O **Estado da Conta** deve ter um símbolo de marca de verificação verde.

A Cloudyn começa a recolher os dados e a povoar os relatórios. Em seguida, [ative a faturação detalhada do AWS](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Ativar a faturação detalhada do AWS

Utilize os passos seguintes para obter o ARN da Função do AWS. Pode utilizar o ARN da Função para conceder permissões de leitura a um registo de faturação.

1. Inicie sessão na consola do AWS em [https://console.aws.amazon.com](https://console.aws.amazon.com) e selecione **Serviços**.
2. Na caixa Pesquisa de Serviços, escreva *IAM* e selecione essa opção.
3. Selecione **Funções** no menu esquerdo.
4. Na lista de Funções, selecione a função que criou para o acesso à Cloudyn.
5. Na página Resumo de Funções, clique para copiar o **ARN da Função**. Mantenha o ARN da Função disponível para os passos posteriores.

### <a name="create-an-s3-bucket"></a>Criar um registo S3

Pode criar um registo S3 para armazenar informações de faturação detalhadas.

1. Inicie sessão na consola do AWS em [https://console.aws.amazon.com](https://console.aws.amazon.com) e selecione **Serviços**.
2. Na caixa Pesquisa de Serviços, escreva *S3* e selecione **S3**.
3. Na página Amazon S3, clique em **Criar registo**.
4. No assistente Criar registo, escolha o Nome e a Região do registo e clique em **Seguinte**.  
    ![informações de exemplo na página Criar registo](./media/connect-aws-account/create-bucket.png)
5. Na página **Definir propriedades**, mantenha os valores predefinidos e clique em **Seguinte**.
6. Na página Rever, clique em **Criar registo**. É apresentada a lista de registos.
7. Clique no registo que criou, selecione o separador **Permissões** e, em seguida, selecione **Política de Registo**. É aberto o editor de Políticas de registo.
8. Copie o exemplo de JSON seguinte e cole-o no Editor de políticas de registo.
   - Substitua `<BillingBucketName>` pelo nome do registo S3.
   - Substitua `<ReadOnlyUserOrRole>` pelo ARN da Função ou do Utilizador que já tinha copiado anteriormente.

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
    ![clicar em Guardar no Editor de políticas de registo](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Ativar relatórios de faturação do AWS

Depois de criar e configurar o registo S3, navegue para [Preferências de Faturação](https://console.aws.amazon.com/billing/home?#/preference) na consola do AWS.

1. Na página Preferências, selecione **Receber Relatórios de Faturação**.
2. Em **Receber Relatórios de Faturação**, introduza o nome do registo que criou e clique em **Verificar**.  
3. Selecione as quatro opções de granularidade do relatório e clique em **Guardar preferências**.  
    ![selecionar a granularidade para ativar os relatórios](./media/connect-aws-account/enable-reports.png)

A Cloudyn obtém informações de faturação detalhadas do registo S3 e povoa os relatórios após a faturação detalhada ser ativada. Pode demorar até 24 horas para que os dados de faturação detalhados sejam apresentados na consola da Cloudyn. Quando estiverem disponíveis dados de faturação detalhados, o estado de consolidação da conta é apresentado como **Consolidado**. O estado da conta é apresentado como **Concluído**.

![estado de consolidação apresentado no separador Contas do AWS](./media/connect-aws-account/consolidated-status.png)

Alguns dos relatórios de otimização podem requerer alguns dias de dados para obter um tamanho de dados de exemplo adequado para fornecer recomendações precisas.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a Cloudyn, continue para o tutorial [Rever a utilização e os custos](tutorial-review-usage.md) da Cloudyn.
