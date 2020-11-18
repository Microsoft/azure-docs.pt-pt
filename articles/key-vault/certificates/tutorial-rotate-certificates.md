---
title: Tutorial - Atualização da frequência de rotação automática do certificado no Cofre-Chave Microsoft Docs
description: Tutorial mostrando como atualizar a frequência de rotação automática de um certificado no Cofre de Chaves Azure usando o portal Azure
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
ms.openlocfilehash: 591d71e8cd6af2801540f5a1a41ad88b1f538e81
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844493"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Tutorial: Configurar o certificado autorrotação no Cofre de Chaves

Você pode facilmente providenciar, gerir e implementar certificados digitais usando Azure Key Vault. Os certificados podem ser certificados de segurança pública e privada (SSL)/Transport Layer Security (TLS) assinados por uma autoridade de certificados (CA), ou um certificado auto-assinado. A Key Vault também pode solicitar e renovar certificados através de parcerias com CAs, fornecendo uma solução robusta para a gestão do ciclo de vida de certificados. Neste tutorial, irá atualizar o período de validade de um certificado, a frequência de rotação automática e os atributos ca.

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Gerir um certificado utilizando o portal Azure.
> * Adicione uma conta de provedor de CA.
> * Atualize o período de validade do certificado.
> * Atualize a frequência de rotação automática do certificado.
> * Atualize os atributos do certificado utilizando a Azure PowerShell.

Antes de começar, leia [os conceitos básicos do Key Vault.](../general/basic-concepts.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

Crie um cofre de chaves Azure utilizando [o portal Azure,](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)ou [Azure PowerShell](../general/quick-create-powershell.md). No exemplo, o nome do cofre-chave é **Exemplo-Vault.**

![Saída após a criação do cofre chave termina](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Criar um certificado em Key Vault

Crie um certificado ou importe um certificado para o cofre chave (ver [Passos para criar um certificado no Cofre-Chave).](../secrets/quick-create-portal.md) Neste caso, trabalhará num certificado chamado **ExemploCertificado.**

## <a name="update-certificate-lifecycle-attributes"></a>Atualizar atributos de ciclo de vida certificado

No Azure Key Vault, pode atualizar os atributos do ciclo de vida de um certificado antes e depois da hora de criação do certificado.

Um certificado criado no Cofre-Chave pode ser:

- Um certificado auto-assinado.
- Um certificado criado com um CA que é parceiro com o Key Vault.
- Um certificado com um ac que não é uma parceria com o Key Vault.

Os seguintes CAs são atualmente fornecedores em parceria com o Key Vault:

- DigiCert: Key Vault oferece certificados OV TLS/SSL.
- GlobalSign: Key Vault oferece certificados OV TLS/SSL.

Key Vault auto-rota certificados através de parcerias estabelecidas com CAs. Como o Key Vault solicita e renova automaticamente certificados através da parceria, a capacidade de rotação automática não é aplicável para certificados criados com CAs que não são associados com Key Vault.

> [!NOTE]
> Um administrador de conta para um fornecedor de CA cria credenciais que o Key Vault usa para criar, renovar e usar certificados TLS/SSL.
![Autoridade de certificação](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Atualizar atributos de ciclo de vida certificado no momento da criação

1. Nas páginas das propriedades do Cofre-Chave, selecione **Certificados**.
1. **Selecione Gerar/Importar.**
1. No ecrã de **certificados,** atualize os seguintes valores:

   - **Período de validade**: Insira o valor (em meses). A criação de certificados de curta duração é uma prática de segurança recomendada. Por padrão, o valor de validade de um certificado recém-criado é de 12 meses.
   - **Tipo de ação vitalícia**: Selecione a ação de renovação automática e alerta do certificado e, em seguida, atualize **a percentagem** de vida útil ou **o número de dias antes de expirar**. Por defeito, a renovação automática de um certificado é fixada em 80% da sua vida útil. A partir do menu suspenso, selecione uma das seguintes opções.

        |  Renovar automaticamente num dado momento| Envie por e-mail todos os contactos num dado momento |
        |-----------|------|
        |A seleção desta opção *ligará a* autoração. | A seleção desta opção *não* rodará automaticamente, mas apenas alertará os contactos.|

1. Selecione **Criar**.

![Ciclo de vida certificado](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Atualizar atributos de ciclo de vida de um certificado armazenado

1. Selecione o cofre da chave.
1. Nas páginas das propriedades do Cofre-Chave, selecione **Certificados**.
1. Selecione o certificado que pretende atualizar. Neste caso, trabalhará num certificado chamado **ExemploCertificado.**
1. Selecione a Política de **Emissão** da barra de menu superior.

   ![Screenshot que realça o botão Demissão Política.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. No ecrã **da Política de Emissão,** atualize os seguintes valores:

   - **Período de validade**: Atualizar o valor (em meses).
   - **Tipo de Ação vitalícia**: Selecione a ação de renovação automática e alerta do certificado e, em seguida, atualize a **percentagem** de vida útil ou **o número de dias antes de expirar**.

   ![Propriedades de certificados](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Selecione **Guardar**.

> [!IMPORTANT]
> Alterar o Tipo de Ação Vitalícia para um certificado registará imediatamente modificações para os certificados existentes.


### <a name="update-certificate-attributes-by-using-powershell"></a>Atualizar atributos de certificado utilizando PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Para modificar a política de renovação de uma lista de certificados, `File.csv` insira `VaultName,CertName` como no seguinte exemplo:
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
Para saber mais sobre os parâmetros, consulte [o certificado az keyvault](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais do Key Vault baseiam-se neste tutorial. Se planeia trabalhar com estes tutoriais, talvez queira deixar estes recursos existentes no lugar.
Quando já não precisar, elimine o grupo de recursos, que elimina o cofre-chave e os recursos conexos.

Para eliminar o grupo de recursos utilizando o portal:

1. Insira o nome do seu grupo de recursos na caixa **de Pesquisa** no topo do portal. Quando o grupo de recursos utilizado neste arranque rápido aparecer nos resultados da pesquisa, selecione-o.
1. Selecione **Eliminar grupo de recursos**.
1. No **TIPO O NOME DO GRUPO DE RECURSOS:** caixa, digite o nome do grupo de recursos e, em seguida, selecione **Delete**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atualizou os atributos do ciclo de vida de um certificado. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os seguintes artigos:

- Leia mais sobre [a criação de certificados de gestão no Cofre chave Azure.](./create-certificate-scenarios.md)
- Reveja a [visão geral do cofre de chaves](../general/overview.md).
