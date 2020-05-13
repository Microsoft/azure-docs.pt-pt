---
title: Tutorial - Atualizar frequência de rotação automática de certificado no Cofre chave [ Microsoft Docs
description: Tutorial mostrando como atualizar a frequência de rotação automática de um certificado no Cofre chave Azure usando o portal Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: eeceb1279579055bfff33f0a4413f0798418faed
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201517"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Tutorial: Configure certificado de rotação automática em Cofre chave

Pode fornecer, gerir e implementar facilmente certificados digitais utilizando o Azure Key Vault. Os certificados podem ser certificados públicos e privados de camada de tomadas seguras (SSL)/Transport Layer Security (TLS) assinados por uma autoridade de certificados (CA), ou um certificado auto-assinado. A Key Vault também pode solicitar e renovar certificados através de parcerias com os CAs, fornecendo uma solução robusta para a gestão do ciclo de vida do certificado. Neste tutorial, irá atualizar o período de validade de um certificado, frequência de rotação automática e atributos CA.

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Gerencie um certificado utilizando o portal Azure.
> * Adicione uma conta de fornecedor de CA.
> * Atualize o período de validade do certificado.
> * Atualize a frequência de rotação automática do certificado.
> * Atualize os atributos do certificado utilizando o Azure PowerShell.

Antes de começar, leia [os conceitos básicos do Key Vault.](../general/basic-concepts.md)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

Crie um cofre chave ou selecione o seu cofre existente para realizar operações (consulte [Passos para criar um cofre chave).](../quick-create-portal.md) No exemplo, o nome chave do cofre é **Exemplo-Vault**.

![Saída após acabamentos chave da criação do cofre](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Criar um certificado no Cofre chave

Crie um certificado ou importe um certificado no cofre chave (ver [Passos para criar um certificado no Cofre chave).](../quick-create-portal.md) Neste caso, trabalhará num certificado chamado **ExemploCertificado**.

## <a name="update-certificate-lifecycle-attributes"></a>Atualização de atributos de ciclo de vida do certificado

No Cofre de Chaves Azure, pode atualizar os atributos de um certificado antes e depois da data da criação do certificado.

Um certificado criado no Cofre chave pode ser:

- Um certificado auto-assinado.
- Um certificado criado com um CA que é associado à Key Vault.
- Um certificado com um ac que não é associado ao Key Vault.

Os seguintes CA são atualmente fornecedores parceiros com a Key Vault:

- DigiCert: Key Vault oferece certificados OV TLS/SSL.
- GlobalSign: Key Vault oferece certificados OV TLS/SSL.

Key Vault roda certificados através de parcerias estabelecidas com CAs. Uma vez que o Key Vault solicita e renova automaticamente os certificados através da parceria, a capacidade de rotação automática não é aplicável para certificados criados com CAs que não são parceiros com a Key Vault.

> [!NOTE]
> Um administrador de conta para um fornecedor de CA cria credenciais que o Key Vault usa para criar, renovar e usar certificados TLS/SSL.
![Autoridade de certificação](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Atualizar atualização atributos de ciclo de vida no momento da criação

1. Nas páginas de propriedades do Cofre-Chave, selecione **Certificados**.
1. **Selecione Generate/Import**.
1. No ecrã **Criar um certificado,** atualize os seguintes valores:

   - **Período de validade**: Insira o valor (em meses). A criação de certificados de curta duração é uma prática de segurança recomendada. Por predefinição, o valor de validade de um certificado recém-criado é de 12 meses.
   - **Tipo**de ação vitalícia : Selecione a ação de renovação automática e alerta do certificado e, em seguida, atualize **a percentagem** de vida ou **o número de dias antes do termo**. Por padrão, a renovação automática de um certificado é fixada em 80% da sua vida útil. A partir do menu suspenso, selecione uma das seguintes opções.

        |  Renovar automaticamente num dado momento| Envie um e-mail a todos os contactos num dado momento |
        |-----------|------|
        |A seleção desta opção *irá ligar* a rotação automática. | A seleção desta opção *não* girará automaticamente, mas apenas alertará os contactos.|

1. Selecione **Criar**.

![Ciclo de vida do certificado](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Atualizar atributos de ciclo de vida de um certificado armazenado

1. Selecione o cofre da chave.
1. Nas páginas de propriedades do Cofre-Chave, selecione **Certificados**.
1. Selecione o certificado que pretende atualizar. Neste caso, trabalhará num certificado chamado **ExemploCertificado**.
1. Selecione Política de **Emissão** a partir da barra de menu superior.

   ![Propriedades de certificado](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. No ecrã política de **emissão,** atualize os seguintes valores:

   - **Período de validade**: Atualizar o valor (em meses).
   - **Tipo**de ação vitalícia : Selecione a ação de renovação automática e alerta do certificado e, em seguida, atualize a **percentagem** de vida ou **número de dias antes do termo**.

   ![Propriedades de certificado](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Selecione **Guardar**.

> [!IMPORTANT]
> A alteração do Tipo de Ação Vitalícia para um certificado registará imediatamente modificações para os certificados existentes.


### <a name="update-certificate-attributes-by-using-powershell"></a>Atualização de atributos de certificado usando PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Para modificar a política de renovação de uma lista de certificados, introduza `File.csv` `VaultName,CertName` como no seguinte exemplo:
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Para saber mais sobre os parâmetros, consulte o [certificado az keyvault](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Limpar recursos

Outros tutoriais key vault baseiam-se neste tutorial. Se planeia trabalhar com estes tutoriais, talvez queira deixar estes recursos existentes no lugar.
Quando já não precisar deles, elimine o grupo de recursos, que elimina o cofre chave e os recursos conexos.

Para eliminar o grupo de recursos utilizando o portal:

1. Insira o nome do seu grupo de recursos na caixa **de pesquisa** no topo do portal. Quando o grupo de recursos utilizado neste quickstart aparecer nos resultados da pesquisa, selecione-o.
1. Selecione **Eliminar grupo de recursos**.
1. No TIPO O NOME DO **GRUPO DE RECURSOS:** caixa, digite o nome do grupo de recursos e, em seguida, selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atualizou os atributos do ciclo de vida de um certificado. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os seguintes artigos:

- Leia mais sobre [gestão da criação de certificados no Cofre chave Azure.](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Reveja a visão geral do [cofre da chave.](../general/overview.md)