---
title: Configurar contas de armazenamento para o Cloudyn no Azure | Documentos da Microsoft
description: Este artigo descreve como configurar contas de armazenamento do Azure e classificações de armazenamento AWS para o Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: 1ac4442aa5a7e5e4367a03d33169412d37b3f1ea
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229928"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Configurar contas de armazenamento para o Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Pode salvar relatórios do Cloudyn no Cloudyn portal, o armazenamento do Azure ou AWS classificações de armazenamento. A guardar os seus relatórios para o portal da Cloudyn é sem encargos. No entanto, a guardar os seus relatórios para o armazenamento do seu fornecedor de serviços de nuvem é opcional e incorre em custos adicionais. Este artigo ajuda-o a configurar contas de armazenamento do Azure e grupos de armazenamento do Amazon Web Services (AWS) para armazenar os seus relatórios.

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma conta de armazenamento do Azure ou um bucket de armazenamento do Amazon.

Se não tiver uma conta de armazenamento do Azure, terá de criar uma. Para obter mais informações sobre como criar uma conta de armazenamento do Azure, consulte [criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

Se não tiver um AWS bucket de serviço (S3) de armazenamento simples, precisa criar um. Para obter mais informações sobre como criar um Bucket S3, consulte [criar um Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Configurar a sua conta de armazenamento do Azure

A configurar o armazenamento do Azure para utilização por Cloudyn é simples. Recolher detalhes sobre a conta de armazenamento e copiá-los no portal do Cloudyn.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **todos os serviços**, selecione **contas de armazenamento**, role até a conta de armazenamento que você deseja usar e, em seguida, selecione a conta.
3. Na página da sua conta de armazenamento em **configurações**, clique em **chaves de acesso**.
4. Copie o **nome da conta de armazenamento** e a **cadeia de conexão** em key1.  
   ![copiar o nome da conta de armazenamento e a cadeia de conexão](./media/storage-accounts/azure-storage-access-keys.png)  
5. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com e inicie sessão.
6. Clique no símbolo engrenagem e selecione **relatórios gerenciamento de armazenamento**.
7. Clique em **Adicionar novo +** e verifique se Microsoft Azure está selecionado. Cole o nome da conta de armazenamento do Azure na área **nome** . Cole a **cadeia de conexão** na área correspondente. Insira um nome de contêiner e, em seguida, clique em **salvar**.  
   ![colar o nome da conta de armazenamento do Azure e a cadeia de conexão na caixa de armazenamento adicionar um novo relatório](./media/storage-accounts/azure-cloudyn-storage.png)

   A nova entrada de armazenamento do Azure do relatório é apresentada na lista conta de armazenamento.  
    ![Nova entrada de armazenamento do Azure do relatório na lista](./media/storage-accounts/azure-storage-entry.png)


Agora pode salvar relatórios para o armazenamento do Azure. Em qualquer relatório, clique em **ações** e selecione **agendar relatório**. Nome do relatório e adicione o seu próprio URL ou utilize o URL criado automaticamente. Selecione **salvar no armazenamento** e, em seguida, selecione a conta de armazenamento. Introduza um prefixo que é anexado ao nome do ficheiro de relatório. Selecione o formato de ficheiro CSV ou JSON e, em seguida, guarde o relatório.

## <a name="configure-an-aws-storage-bucket"></a>Configurar um bucket de armazenamento AWS

O Cloudyn utiliza credenciais do AWS existentes: utilizador ou função, para guardar os relatórios ao seu registo. Para testar o acesso, o Cloudyn tenta salvar um pequeno arquivo de texto no Bucket com o nome de arquivo _check-Bucket-Permission. txt_.

Forneça o utilizador ou função do Cloudyn com a permissão de PutObject ao seu registo. Em seguida, utilizar um registo existente ou criar um novo para guardar relatórios. Por fim, decida como gerir a classe de armazenamento, definir regras de ciclo de vida ou remover arquivos desnecessários.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Atribuir permissões a sua função ou utilizador do AWS

Quando cria uma nova política, fornecer as permissões exatas necessárias para guardar um relatório para um registo de S3.

1. Entre no console do AWS e selecione **Serviços**.
2. Selecione **iam** na lista de serviços.
3. Selecione **políticas** no lado esquerdo do console e clique em **criar política**.
4. Clique na guia **JSON** .
5. A seguinte política permite-lhe guardar um relatório para um registo de S3. Copie e cole o exemplo de política a seguir na guia **JSON** . Substitua &lt;bucketname&gt; pelo nome do Bucket.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Clique em **examinar política**.  
    ![a política JSON AWS mostrando informações de exemplo](./media/storage-accounts/aws-policy.png)  
7. Na página de política de revisão, escreva um nome para a sua política. Por exemplo, _CloudynSaveReport2S3_.
8. Clique em **criar política**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Anexar a política a uma função de Cloudyn ou o utilizador na sua conta

Para anexar a nova política, abra a consola do AWS e editar o utilizador ou função do Cloudyn.

1. Entre no console do AWS e selecione **Serviços**e, em seguida, selecione **iam** na lista de serviços.
2. Selecione **funções** ou **usuários** no lado esquerdo do console.

**Para funções:**

  1. Clique no nome de função do Cloudyn.
  2. Na guia **permissões** , clique em **anexar política**.
  3. Pesquise a política que você criou e selecione-a e, em seguida, clique em **anexar política**.
    ![a política de exemplo anexada à sua função Cloudyn](./media/storage-accounts/aws-attach-policy-role.png)

**Para usuários:**

1. Selecione o utilizador do Cloudyn.
2. Na guia **permissões** , clique em **adicionar permissões**.
3. Na seção **conceder permissão** , selecione **anexar políticas existentes diretamente**.
4. Pesquise a política que você criou e selecione-a e clique em **Avançar: examinar**.
5. Na página adicionar permissões à função nome, clique em **adicionar permissões**.  
    ![política de exemplo anexada ao usuário Cloudyn](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Opcional: Definir a permissão com a política de registo

Também pode definir a permissão para criar relatórios no seu registo de S3 utilizando uma política de registo. Na vista de S3 clássica:

1. Crie ou selecione um registo existente.
2. Selecione a guia **permissões** e, em seguida, clique em **política de Bucket**.
3. Copie e cole o seguinte exemplo de política. Substitua &lt;Bucket\_nome&gt; e &lt;Cloudyn\_princípio&gt; com o ARN de seu Bucket. Substitua o ARN da função ou utilizador utilizado pela Cloudyn.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. No editor de política de Bucket, clique em **salvar**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Adicionar armazenamento de relatório do AWS ao Cloudyn

1. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com e inicie sessão.
2. Clique no símbolo engrenagem e selecione **relatórios gerenciamento de armazenamento**.
3. Clique em **Adicionar novo +** e verifique se AWS está selecionado.
4. Selecione um registo de conta e de armazenamento. O nome do registo de armazenamento de AWS é automaticamente preenchido-in.  
    ![Informações de exemplo no adicionar uma nova caixa de armazenamento de relatório](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Clique em **salvar** e em **OK**.

    A nova entrada do armazenamento de relatório do AWS é apresentada na lista conta de armazenamento.  
    ![AWS novo relatório show de entrada de armazenamento na lista de conta de armazenamento](./media/storage-accounts/aws-storage-entry.png)


Agora pode salvar relatórios para o armazenamento do Azure. Em qualquer relatório, clique em **ações** e selecione **agendar relatório**. Nome do relatório e adicione o seu próprio URL ou utilize o URL criado automaticamente. Selecione **salvar no armazenamento** e, em seguida, selecione a conta de armazenamento. Introduza um prefixo que é anexado ao nome do ficheiro de relatório. Selecione o formato de ficheiro CSV ou JSON e, em seguida, guarde o relatório.

## <a name="next-steps"></a>Passos seguintes

- Examine [noções básicas sobre relatórios do Cloudyn](understanding-cost-reports.md) para saber mais sobre a estrutura básica e as funções de relatórios do Cloudyn.
