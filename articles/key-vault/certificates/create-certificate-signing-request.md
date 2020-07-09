---
title: Criação e fusão de CSR no Cofre da Chave Azure
description: Criação e fusão de CSR no Cofre da Chave Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: ae2d6259bac6a2034edc98de9b0405f32f17fbc3
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849485"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Criação e fusão de CSR no Cofre de Chaves

O Azure Key Vault suporta o armazenamento de certificados digitais emitidos por qualquer Autoridade de Certificados à sua escolha no seu cofre principal. Apoia a criação do pedido de assinatura de certificado com o par chave público-privado que pode ser assinado por qualquer Autoridade de Certificados escolhida. Pode ser uma empresa interna ca ou ca público externo. Um pedido de assinatura de certificado (também CSR ou pedido de certificação) é uma mensagem enviada pelo utilizador a uma autoridade de certificados (CA) a fim de solicitar a emissão de um certificado digital.

Para obter informações mais gerais sobre certificados, consulte [os certificados de cofre chave Azure.](/azure/key-vault/certificates/about-certificates)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>Adicionar certificado em Cofre-Chave emitido por um CA não confiável

Os seguintes passos irão ajudá-lo a criar um certificado das autoridades de certificados que não são parceiros com o Key Vault (por exemplo, o GoDaddy não é um cofre de confiança CA) 


### <a name="azure-powershell"></a>Azure PowerShell



1.  Em primeiro lugar, **crie a política de certificados.** O Key Vault não inscreverá ou renovará o certificado do Emitente em nome do utilizador como CA escolhido neste cenário não é suportado e, portanto, o EmitenteName está definido para Desconhecido.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Criar um pedido de **assinatura de certificado**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Obtenção do **pedido** de RSE assinado pela AC `$certificateOperation.CertificateSigningRequest` É o pedido de assinatura do certificado codificado base4 para o certificado. Você pode pegar esta bolha e despejar no site de pedido de certificado do Emitente. Este passo varia de CA para CA, a melhor maneira seria procurar as diretrizes da sua AC sobre como executar este passo. Também pode utilizar ferramentas como certreq ou openssl para obter o pedido de certificado assinado e completar o processo de geração de um certificado.


4. **Fusão do pedido assinado** no Cofre-Chave Depois de o pedido de certificado ter sido assinado pelo Emitente, pode trazer de volta o certificado assinado e fundi-lo com o par inicial de chaves público-privado criado no Cofre chave Azure

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    O pedido de certificado foi agora fundido com sucesso.

### <a name="azure-portal"></a>Portal do Azure

1.  Para gerar CSR para o CA à sua escolha, navegue até ao cofre Key que pretende adicionar o certificado.
2.  Nas páginas das propriedades do Cofre-Chave, selecione **Certificados**.
3.  **Selecione o separador Gerar/Importar.**
4.  No **Ecrã de Certificado escolha** os seguintes valores:
    - **Método de Criação de Certificados:** Gerar.
    - **Nome do certificado:** ContosoManualCSRCertificate.
    - **Tipo de Autoridade de Certificados (CA):** Certificado emitido por uma AC não integrada
    - **Objeto:**`"CN=www.contosoHRApp.com"`
    - Selecione os outros valores conforme desejado. Clique em **Criar**.

    ![Propriedades de certificados](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  Verá que o certificado foi agora adicionado na lista de Certificados. Selecione este novo certificado que tinha acabado de criar. O estado atual do certificado seria "desativado", uma vez que ainda não foi emitido pela AC.
7. Clique no separador **Operação certificado** e selecione **Download CSR**.
 ![Propriedades de certificados](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Leve o ficheiro .csr à AC para que o pedido seja assinado.
9.  Uma vez que o pedido seja assinado pela AC, traga de volta o ficheiro de certificado para **fundir o pedido assinado** no mesmo ecrã de Operação certificado.

O pedido de certificado foi agora fundido com sucesso.

## <a name="troubleshoot"></a>Resolução de problemas

Se o certificado emitido estiver em estado de 'desactivado' no portal Azure, consulte a **Operação certificado** para rever a mensagem de erro desse certificado.

Para obter mais informações, consulte as operações do [Certificado na referência API do Cofre-Chave](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Abóbadas - Atualizar a Política de Acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Passos seguintes

- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
