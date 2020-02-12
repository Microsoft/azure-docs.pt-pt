---
title: Configurar contas de armazenamento para a Cloudyn no Azure | Microsoft Docs
description: Este artigo descreve como configurar contas de armazenamento do Azure e registos de armazenamento do AWS para a Cloudyn.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: secdec18
ms.openlocfilehash: aa5fc1dddec6931b5eff1e34cf6c12b218bdf2fd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76769964"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Configurar contas de armazenamento para a Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Pode guardar os relatórios da Cloudyn no portal da Cloudyn, no armazenamento do Azure ou nos registos de armazenamento do AWS. Guardar relatórios no portal da Cloudyn é gratuito. No entanto, guardar relatórios no armazenamento do fornecedor de serviços cloud é opcional e incorre em custos adicionais. Este artigo ajuda-o a configurar contas de armazenamento do Azure e registos de armazenamento do Amazon Web Services (AWS) para armazenar os relatórios.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter uma conta de armazenamento do Azure ou um registo de armazenamento do Amazon.

Se não tiver uma conta de armazenamento do Azure, terá de criar uma. Para obter mais informações sobre como criar contas de armazenamento do Azure, veja [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

Se não tiver um registo do Simple Storage Service (S3) do AWS, terá de criar um. Para obter mais informações sobre como criar um registo S3, veja [Criar um Registo](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Configurar a conta de armazenamento do Azure

A configuração do armazenamento do Azure para utilização pela Cloudyn é simples. Reúna os detalhes sobre a conta de armazenamento e copie-os no portal da Cloudyn.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **Todos os Serviços**, selecione **Contas de armazenamento**, desloque-se para a conta de armazenamento que quer utilizar e, em seguida, selecione a conta.
3. Na página da conta de armazenamento, em **Definições**, clique em **Chaves de Acesso**.
4. Copie o **Nome da conta de armazenamento** e a **Cadeia de ligação** em key1.  
   ![Copiar o nome da conta de armazenamento e a cadeia de ligação](./media/storage-accounts/azure-storage-access-keys.png)  
5. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com e inicie sessão.
6. Clique no símbolo de engrenagem e, em seguida, selecione **Gestão de Armazenamento de Relatórios**.
7. Clique em **Adicionar novo +** e verifique se o Microsoft Azure está selecionado. Cole o nome da conta de armazenamento do Azure na área **Nome**. Cole a **cadeia de ligação** na área correspondente. Introduza um nome de contentor e, em seguida, clique em **Guardar**.  
   ![Colar o nome da conta de armazenamento do Azure e a cadeia de ligação na caixa Adicionar novo armazenamento de relatórios](./media/storage-accounts/azure-cloudyn-storage.png)

   A nova entrada de armazenamento de relatórios do Azure aparece na lista de contas de armazenamento.  
    ![Nova entrada de armazenamento de relatórios do Azure na lista](./media/storage-accounts/azure-storage-entry.png)


Pode agora guardar relatórios no armazenamento do Azure. Em qualquer relatório, clique em **Ações** e, em seguida, selecione **Agendar relatório**. Atribua um nome ao relatório e, em seguida, adicione o seu próprio URL ou utilize o URL criado automaticamente. Selecione **Guardar no armazenamento** e, em seguida, selecione a conta de armazenamento. Introduza um prefixo que seja anexado ao nome de ficheiro do relatório. Selecione o formato de ficheiro CSV ou JSON e, em seguida, guarde o relatório.

## <a name="configure-an-aws-storage-bucket"></a>Configurar um registo de armazenamento do AWS

A Cloudyn utiliza as credenciais do AWS existentes: Utilizador ou Função, para guardar os relatórios no registo. Para testar o acesso, a Cloudyn tenta guardar um pequeno ficheiro de texto no registo com o nome de ficheiro _check-bucket-permission.txt_.

Forneça a função ou utilizador da Cloudyn com a permissão PutObject no registo. Em seguida, utilize um registo existente ou crie um novo para guardar os relatórios. Por fim, decida como gerir a classe de armazenamento, defina as regras do ciclo de vida ou remova quaisquer ficheiros desnecessários.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Atribuir permissões ao utilizador ou à função do AWS

Quando cria uma nova política, fornece as permissões exatas necessárias para guardar um relatório num registo S3.

1. Inicie sessão na consola do AWS e selecione **Serviços**.
2. Selecione **IAM** na lista de serviços.
3. Selecione **Políticas** no lado esquerdo da consola e, em seguida, clique em **Criar Política**.
4. Clique no separador **JSON**.
5. A política seguinte permite-lhe guardar um relatório num registo S3. Copie e cole o exemplo de política seguinte no separador **JSON**. Substitua &lt;bucketname&gt; pelo nome do registo.

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

6. Clique em **Política de revisão**.  
    ![Política JSON do AWS a mostrar informações de exemplo](./media/storage-accounts/aws-policy.png)  
7. Na página Política de revisão, escreva um nome para a política. Por exemplo, _CloudynSaveReport2S3_.
8. Clique em **Criar política**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Anexar a política a uma função ou um utilizador da Cloudyn na conta

Para anexar a nova política, abra a consola do AWS e edite a função ou o utilizador da Cloudyn.

1. Inicie sessão na consola do AWS, selecione **Serviços** e, em seguida, selecione **IAM** na lista de serviços.
2. Selecione **Funções** ou **Utilizadores** no lado esquerdo da consola.

**Para funções:**

  1. Clique no nome da função da Cloudyn.
  2. No separador **Permissões**, clique em **Anexar Política**.
  3. Procure a política que criou, selecione-a e, em seguida, clique em **Anexar Política**.
    ![Política de exemplo anexada à função da Cloudyn](./media/storage-accounts/aws-attach-policy-role.png)

**Para utilizadores:**

1. Selecione o Utilizador da Cloudyn.
2. No separador **Permissões**, clique em **Adicionar permissões**.
3. Na secção **Conceder Permissão**, selecione **Anexar políticas existentes diretamente**.
4. Procure a política que criou, selecione-a e, em seguida, clique em **Seguinte: Revisão**.
5. Na página Adicionar permissões ao nome da função, clique em **Adicionar permissões**.  
    ![Política de exemplo anexada ao utilizador da Cloudyn](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Opcional: Definir a permissão com a política de registo

Também pode definir uma permissão para criar relatórios no registo S3 com uma política de registo. Na vista S3 clássica:

1. Crie ou selecione um registo existente.
2. Selecione o separador **Permissões** e, em seguida, clique em **Política de registo**.
3. Copie e cole o exemplo de política seguinte. Substitua &lt;bucket\_name&gt; e &lt;Cloudyn\_principle&gt; pelo ARN do registo. Substitua o ARN da função ou o utilizador utilizado pela Cloudyn.

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

4. No editor de Políticas de registo, clique em **Guardar**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Adicionar o armazenamento de relatórios do AWS à Cloudyn

1. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com e inicie sessão.
2. Clique no símbolo de engrenagem e, em seguida, selecione **Gestão de Armazenamento de Relatórios**.
3. Clique em **Adicionar novo +** e verifique se o AWS está selecionado.
4. Selecione uma conta e um registo de armazenamento. O nome do registo de armazenamento do AWS é preenchido automaticamente.  
    ![Informações de exemplo na caixa Adicionar novo armazenamento de relatórios](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Clique em **Guardar** e, em seguida, clique em **OK**.

    A nova entrada de armazenamento de relatórios do AWS aparece na lista de contas de armazenamento.  
    ![Nova entrada de armazenamento de relatórios do AWS na lista de contas de armazenamento](./media/storage-accounts/aws-storage-entry.png)


Pode agora guardar relatórios no armazenamento do Azure. Em qualquer relatório, clique em **Ações** e, em seguida, selecione **Agendar relatório**. Atribua um nome ao relatório e, em seguida, adicione o seu próprio URL ou utilize o URL criado automaticamente. Selecione **Guardar no armazenamento** e, em seguida, selecione a conta de armazenamento. Introduza um prefixo que seja anexado ao nome de ficheiro do relatório. Selecione o formato de ficheiro CSV ou JSON e, em seguida, guarde o relatório.

## <a name="next-steps"></a>Passos seguintes

- Veja [Compreender os relatórios da Cloudyn](understanding-cost-reports.md) para conhecer a estrutura básica e as funções dos relatórios da Cloudyn.
