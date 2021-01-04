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
ms.openlocfilehash: 42f649f9dd206b34f0fac8513ba742febed2dbcb
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724634"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Criação e fusão de CSR no Cofre de Chaves

O Azure Key Vault suporta o armazenamento de certificados digitais emitidos por qualquer Autoridade de Certificados à sua escolha no seu cofre principal. Apoia a criação do pedido de assinatura de certificado com o par chave público-privado que pode ser assinado por qualquer Autoridade de Certificados escolhida. Pode ser uma empresa interna ca ou ca público externo. Um pedido de assinatura de certificado (também CSR ou pedido de certificação) é uma mensagem enviada pelo utilizador a uma autoridade de certificados (CA) a fim de solicitar a emissão de um certificado digital.

Para obter informações mais gerais sobre certificados, consulte [os certificados de cofre chave Azure.](./about-certificates.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="adding-certificate-in-key-vault-issued-by-partnered-ca"></a>Adicionar certificado no Cofre-Chave emitido pela AC parceira
Parceiros-chave da Vault com a seguinte a duas autoridades de certificados para simplificar a criação de certificados. 

|Fornecedor|Tipo de certificado|Configuração de configuração  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault oferece certificados OV ou EV SSL com DigiCert| [Guia de Integração](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault oferece certificados OV ou EV SSL com GlobalSign| [Guia de Integração](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="adding-certificate-in-key-vault-issued-by-non-partnered-ca"></a>Adicionar certificado no Cofre-Chave emitido por CA não parceiro

Os seguintes passos irão ajudá-lo a criar um certificado das autoridades de certificados que não são parceiros com o Key Vault (por exemplo, o GoDaddy não é um cofre de confiança CA) 



# <a name="portal"></a>[Portal](#tab/azure-portal)

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

   ![Screenshot que realça o botão Download CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)
 
8.  Leve .csr arquivo para a AC para o pedido de assinatura.
9.  Uma vez que o pedido seja assinado pela AC, traga de volta o ficheiro de certificado para **fundir o pedido assinado** no mesmo ecrã de Operação certificado.

O pedido de certificado foi agora fundido com êxito.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)



1. Em primeiro lugar, **crie a política de certificados.** O Key Vault não inscreverá ou renovará o certificado do Emitente em nome do utilizador como CA escolhido neste cenário não é suportado e, portanto, o EmitenteName está definido para Desconhecido.

   ```azurepowershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
    
   > [!NOTE]
   > Se estiver a utilizar um Nome Distinto Relativo (RDN) que tenha uma vírgula (,)no valor, use aspas individuais e embrulhe o valor que contém o carácter especial em ações duplas. Exemplo: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. Neste exemplo, o `OU` valor é como **Docs, Contoso.** Este formato funciona para todos os valores que contêm uma vírgula.

2. Criar um pedido de **assinatura de certificado**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Obtenção do **pedido** de RSE assinado pela AC `$csr.CertificateSigningRequest` É o pedido de assinatura do certificado codificado base4 para o certificado. Você pode pegar esta bolha e despejar no site de pedido de certificado do Emitente. Este passo varia de CA para CA, a melhor maneira seria procurar as diretrizes da sua AC sobre como executar este passo. Também pode utilizar ferramentas como certreq ou openssl para obter o pedido de certificado assinado e completar o processo de geração de um certificado.


4. **Fusão do pedido assinado** no Cofre-Chave Depois de o pedido de certificado ter sido assinado pelo Emitente, pode trazer de volta o certificado assinado e fundi-lo com o par inicial de chaves público-privado criado no Cofre chave Azure

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    O pedido de certificado foi agora fundido com sucesso.
---

> [!NOTE]
> Se os seus valores RDN tiverem vírgulas, também pode adicioná-las no campo **Assunto,** rodeando o valor em cotações duplas, como mostrado no passo 4.
> Exemplo de entrada para "Assunto": `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com` Neste exemplo, o RDN `OU` contém um valor com uma vírgula no nome. A saída resultante `OU` é **docs, Contoso.**

## <a name="adding-more-information-to-csr"></a>Adicionar mais informações à CSR

Se quiser adicionar mais informações ao criar RSE, por exemplo - 
    - País:
    - Cidade / Localidade:
    - Estado / Província:
    - Organização:
    - Unidade Organizacional: Pode adicionar toda essa informação ao criar uma RSE definindo-a no nome do sujeito.

Exemplo
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

> [!NOTE]
> Se você está solicitando um cert DEV com todos esses detalhes na RSE, a AC pode rejeitar o pedido porque pode não ser capaz de validar todas as informações no pedido. Se está a pedir um certificado de OV, seria mais apropriado adicionar toda essa informação na RSE.


## <a name="troubleshoot"></a>Resolução de problemas

- Para monitorizar ou gerir a resposta do pedido de certificado, saiba mais [aqui](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)

- **Error type 'A chave pública do certificado de entidade final no conteúdo do certificado X.509 especificado não corresponde à parte pública da chave privada especificada. Verifique se o certificado é válido»** Este erro pode ocorrer se não estiver a fundir a RSE com o mesmo pedido de RSE iniciado. Sempre que um CSR é criado, cria uma chave privada cuja correspondência deve ser cumprida ao intercalar o pedido assinado.
    
- Quando a RSE for fundida, fundiria toda a cadeia?
    Sim, irá fundir toda a cadeia, desde que o utilizador tenha trazido de volta o ficheiro P7b para se fundir.

- Se o certificado emitido estiver em estado de 'desactivado' no portal Azure, consulte a **Operação certificado** para rever a mensagem de erro desse certificado.

Para obter mais informações, consulte as operações do [Certificado na referência API do Cofre-Chave](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Abóbadas - Atualizar a Política de Acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

- **Error type 'O nome do sujeito fornecido não é um nome X500 válido'** Este erro pode ocorrer se tiver incluído quaisquer "caracteres especiais" nos valores do Nome Sujeito. Consulte notas no portal Azure e instruções PowerShell respectivamente. 

---
## <a name="next-steps"></a>Passos seguintes

- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
