---
title: Configurar contas de armazenamento para o Cloudyn no Azure | Documentos da Microsoft
description: Este artigo descreve como configurar contas de armazenamento do Azure e classificações de armazenamento AWS para o Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: f03193253bd8d8a7530d65a552a07d3901887cf5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104306"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Configurar contas de armazenamento para o Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Pode salvar relatórios do Cloudyn no Cloudyn portal, o armazenamento do Azure ou AWS classificações de armazenamento. A guardar os seus relatórios para o portal da Cloudyn é sem encargos. No entanto, a guardar os seus relatórios para o armazenamento do seu fornecedor de serviços de nuvem é opcional e incorre em custos adicionais. Este artigo ajuda-o a configurar contas de armazenamento do Azure e grupos de armazenamento do Amazon Web Services (AWS) para armazenar os seus relatórios.

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma conta de armazenamento do Azure ou um bucket de armazenamento do Amazon.

Se não tiver uma conta de armazenamento do Azure, terá de criar uma. Para obter mais informações sobre como criar uma conta de armazenamento do Azure, consulte [criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

Se não tiver um AWS bucket de serviço (S3) de armazenamento simples, precisa criar um. Para obter mais informações sobre como criar um registo de S3, consulte [criar um Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Configurar a sua conta de armazenamento do Azure

A configurar o armazenamento do Azure para utilização por Cloudyn é simples. Recolher detalhes sobre a conta de armazenamento e copiá-los no portal do Cloudyn.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **todos os serviços**, selecione **contas de armazenamento**, desloque-se para a conta de armazenamento que pretende utilizar e, em seguida, selecione a conta.
3. Na sua página de conta de armazenamento em **configurações**, clique em **chaves de acesso**.
4. Copiar seus **nome da conta de armazenamento** e **cadeia de ligação** em chave1.  
   ![Copie a cadeia de ligação e o nome de conta de armazenamento](./media/storage-accounts/azure-storage-access-keys.png)  
5. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com e inicie sessão.
6. Clique no símbolo de engrenagem e, em seguida, selecione **gestão de armazenamento de relatórios**.
7. Clique em **adicionar novo +** e certifique-se de que o Microsoft Azure está selecionada. Cole o seu nome de conta de armazenamento do Azure no **nome** área. Colar seu **cadeia de ligação** na área correspondente. Introduza um nome de contentor e, em seguida, clique em **guardar**.  
   ![Cole o nome e a ligação de cadeias de caracteres no adicionar uma nova caixa de armazenamento de relatório de conta de armazenamento do Azure](./media/storage-accounts/azure-cloudyn-storage.png)

   A nova entrada de armazenamento do Azure do relatório é apresentada na lista conta de armazenamento.  
    ![Nova entrada de armazenamento do Azure do relatório na lista](./media/storage-accounts/azure-storage-entry.png)


Agora pode salvar relatórios para o armazenamento do Azure. Em qualquer relatório, clique em **ações** e, em seguida, selecione **agendar relatório**. Nome do relatório e adicione o seu próprio URL ou utilize o URL criado automaticamente. Selecione **guardar no armazenamento** e, em seguida, selecione a conta de armazenamento. Introduza um prefixo que é anexado ao nome do ficheiro de relatório. Selecione o formato de ficheiro CSV ou JSON e, em seguida, guarde o relatório.

## <a name="configure-an-aws-storage-bucket"></a>Configurar um bucket de armazenamento AWS

O Cloudyn utiliza credenciais do AWS existentes: O utilizador ou função, para guardar os relatórios ao seu registo. Para testar o acesso, tenta salvar um arquivo de texto pequeno para o registo com o nome de ficheiro Cloudyn _bucket de verificação-permission.txt_.

Forneça o utilizador ou função do Cloudyn com a permissão de PutObject ao seu registo. Em seguida, utilizar um registo existente ou criar um novo para guardar relatórios. Por fim, decida como gerir a classe de armazenamento, definir regras de ciclo de vida ou remover arquivos desnecessários.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Atribuir permissões a sua função ou utilizador do AWS

Quando cria uma nova política, fornecer as permissões exatas necessárias para guardar um relatório para um registo de S3.

1. Inicie sessão consola do AWS e selecione **serviços**.
2. Selecione **IAM** na lista de serviços.
3. Selecione **políticas** no lado esquerdo da consola e clique em **criar política**.
4. Clique nas **JSON** separador.
5. A seguinte política permite-lhe guardar um relatório para um registo de S3. Copie e cole o seguinte exemplo de política para o **JSON** separador. Substitua &lt;bucketname&gt; com o nome do registo.

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

6. Clique em **rever política**.  
    ![Política de JSON do AWS, mostrando as informações de exemplo](./media/storage-accounts/aws-policy.png)  
7. Na página de política de revisão, escreva um nome para a sua política. Por exemplo, _CloudynSaveReport2S3_.
8. Clique em **criar política**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Anexar a política a uma função de Cloudyn ou o utilizador na sua conta

Para anexar a nova política, abra a consola do AWS e editar o utilizador ou função do Cloudyn.

1. Inicie sessão consola do AWS e selecione **serviços**, em seguida, selecione **IAM** na lista de serviços.
2. Selecione **funções** ou **utilizadores** do lado esquerdo da consola.

**Para funções:**

  1. Clique no nome de função do Cloudyn.
  2. Sobre o **permissões** separador, clique em **anexar política**.
  3. Procure a política que criou e selecioná-lo, em seguida, clique em **anexar política**.
    ![Política de exemplo ligada à sua função do Cloudyn](./media/storage-accounts/aws-attach-policy-role.png)

**Para os utilizadores:**

1. Selecione o utilizador do Cloudyn.
2. Sobre o **permissões** separador, clique em **adicionar permissões**.
3. Na **conceder permissão** secção, selecione **anexar as políticas existentes diretamente**.
4. Procure a política que criou e selecioná-lo, em seguida, clique em **seguinte: Revisão**.
5. Sobre as permissões de adicionar a página de nome de função, clique em **adicionar permissões**.  
    ![Política de exemplo ligada ao seu utilizador do Cloudyn](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Opcional: Permissão de conjunto com a política de registo

Também pode definir a permissão para criar relatórios no seu registo de S3 utilizando uma política de registo. Na vista de S3 clássica:

1. Crie ou selecione um registo existente.
2. Selecione o **permissões** separador e, em seguida, clique em **política de registo**.
3. Copie e cole o seguinte exemplo de política. Substitua &lt;bucket\_name&gt; e &lt;Cloudyn\_princípio&gt; com ARN do seu registo. Substitua o ARN da função ou utilizador utilizado pela Cloudyn.

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

4. No editor de política de registo, clique em **guardar**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Adicionar armazenamento de relatório do AWS ao Cloudyn

1. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com e inicie sessão.
2. Clique no símbolo de engrenagem e, em seguida, selecione **gestão de armazenamento de relatórios**.
3. Clique em **adicionar novo +** e certifique-se de que o AWS está selecionado.
4. Selecione um registo de conta e de armazenamento. O nome do registo de armazenamento de AWS é automaticamente preenchido-in.  
    ![Informações de exemplo no adicionar uma nova caixa de armazenamento de relatório](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Clique em **salvar** e, em seguida, clique em **Ok**.

    A nova entrada do armazenamento de relatório do AWS é apresentada na lista conta de armazenamento.  
    ![AWS novo relatório show de entrada de armazenamento na lista de conta de armazenamento](./media/storage-accounts/aws-storage-entry.png)


Agora pode salvar relatórios para o armazenamento do Azure. Em qualquer relatório, clique em **ações** e, em seguida, selecione **agendar relatório**. Nome do relatório e adicione o seu próprio URL ou utilize o URL criado automaticamente. Selecione **guardar no armazenamento** e, em seguida, selecione a conta de armazenamento. Introduza um prefixo que é anexado ao nome do ficheiro de relatório. Selecione o formato de ficheiro CSV ou JSON e, em seguida, guarde o relatório.

## <a name="next-steps"></a>Passos Seguintes

- Revisão [relatórios do Cloudyn compreensão](understanding-cost-reports.md) para saber mais sobre a estrutura básica e as funções de relatórios do Cloudyn.
