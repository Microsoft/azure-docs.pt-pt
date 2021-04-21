---
title: Criar e fundir um CSR no Cofre da Chave Azure
description: Aprenda a criar e fundir uma RSE no Cofre da Chave Azure.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a4d079855e5aa05adb84b62d686d9f386608f7bb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752143"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Criar e fundir um CSR no Cofre de Chaves

A Azure Key Vault suporta o armazenamento de certificados digitais emitidos por qualquer autoridade de certificados (CA). Suporta a criação de um pedido de assinatura de certificado (RSE) com um par chave privado/público. O CSR pode ser assinado por qualquer AC (uma empresa interna ca ou um CA público externo). Uma RSE é uma mensagem que envia a uma AC para solicitar um certificado digital.

Para obter informações mais gerais sobre certificados, consulte [os certificados Azure Key Vault](./about-certificates.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Adicionar certificados em Key Vault emitidos por CAs parceiros

Key Vault parceiros com as seguintes autoridades de certificado para simplificar a criação de certificados.

|Fornecedor|Tipo de certificado|Configuração de configuração  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault oferece certificados OV ou EV SSL com DigiCert| [Guia de integração](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault oferece certificados OV ou EV SSL com GlobalSign| [Guia de integração](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Adicionar certificados em Key Vault emitidos por CAs não parceiros

Siga estes passos para adicionar um certificado de CAs que não são parceiros com Key Vault. (Por exemplo, o GoDaddy não é um Cofre de Chaves de confiança CA.)

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Vá ao cofre a que quer adicionar o certificado.
1. Na página de propriedades, selecione **Certificados.**
1. Selecione o **separador Gerar/Importar.**
1. No ecrã **de certificado,** escolha os seguintes valores:
    - **Método de Criação de Certificados**: Gerar.
    - **Denominação do certificado**: ContosoManualCSRCertificate.
    - **Tipo de Certificado Autoridade (CA)**: Certificado emitido por uma AC não integrada.
    - **Objeto**: `"CN=www.contosoHRApp.com"` .
     > [!NOTE]
     > Se estiver a utilizar um Nome Relativo Distinto (RDN) que tenha uma vírgula (,)no valor, embrulhe o valor que contém o carácter especial em ações duplas. 
     >
     > Entrada de exemplo no **sujeito:**`DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > Neste exemplo, o RDN `OU` contém um valor com uma vírgula no nome. A saída resultante `OU` é **docs, Contoso.**
1. Selecione os outros valores conforme desejado e, em seguida, **selecione Criar** para adicionar o certificado à lista **de Certificados.**

    ![Screenshot das propriedades do certificado](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. Na lista **de Certificados,** selecione o novo certificado. O estado atual do certificado está **desativado** porque ainda não foi emitido pela AC.
1. No **separador Operação certificado,** selecione **Download CSR**.

   ![Screenshot que realça o botão Download CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Tenha o sinal de CA da RSE (.csr).
1. Após a assinatura do pedido, selecione **o Pedido Assinado de Fusão** no separador **Operação certificado** para adicionar o certificado assinado ao Cofre-Chave.

O pedido de certificado foi agora fundido com êxito.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Criar uma política de certificados. Como a AC escolhida neste cenário não é parceira, **o EmiterName** está definido para **Desconhecido** e o Cofre-Chave não se inscreve nem renova o certificado.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Se estiver a utilizar um Nome Distinto Relativo (RDN) que tenha uma vírgula (,)no valor, use cotações únicas para o valor completo ou conjunto de valor, e embrulhe o valor que contém o carácter especial em cotações duplas. 
     >
     >Exemplo de entrada no **Nome Sujeito**: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` . Neste exemplo, o `OU` valor é como **Docs, Contoso.** Este formato funciona para todos os valores que contêm uma vírgula.
     > 
     > Neste exemplo, o RDN `OU` contém um valor com uma vírgula no nome. A saída resultante `OU` é **docs, Contoso.**

1. Crie a RSE.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Que o sinal de CA a RSE. Esta `$csr.CertificateSigningRequest` é a base codificada CSR para o certificado. Pode despejar esta bolha no site de pedido de certificado do emitente. Este passo varia de CA para CA. Procure as diretrizes da sua A.C. sobre como executar este passo. Também pode utilizar ferramentas como certreq ou openssl para obter a CSR assinada e completar o processo de geração de um certificado.

1. Funda o pedido assinado no Cofre de Chaves. Após a assinatura do pedido de certificado, pode fundi-lo com o par inicial de chaves privada/pública criada no Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

O pedido de certificado foi agora fundido com êxito.

---

## <a name="add-more-information-to-the-csr"></a>Adicione mais informações à RSE

Se pretender adicionar mais informações ao criar a RSE, defina-a no **Nome de Assunto**. É melhor adicionar informações como:
- País
- Cidade/localidade
- Distrito
- Organização
- Unidade organizacional

Exemplo

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Se estiver a solicitar um certificado de Validação de Domínio (DV) com informações adicionais, a AC poderá rejeitar o pedido se não conseguir validar todas as informações do pedido. As informações adicionais podem ser mais apropriadas se você estiver solicitando um certificado de Validação da Organização (OV).

## <a name="faqs"></a>FAQs

- Como monitorizo ou manco a minha RSE?

     Consulte [monitor e gerencie a criação de certificados.](./create-certificate-scenarios.md)

- E se eu vir **Error type 'A chave pública do certificado de entidade final no conteúdo do certificado X.509 especificado não corresponde à parte pública da chave privada especificada. Por favor, verifique se o certificado é válido'**

     Este erro ocorre se não estiver a fundir a RSE assinada com o mesmo pedido de RSE que iniciou. Cada nova RSE que cria tem uma chave privada, que tem de coincidir quando se funde o pedido assinado.

- Quando uma RSE é fundida, vai fundir toda a cadeia?

     Sim, irá fundir toda a cadeia, desde que o utilizador tenha trazido de volta um ficheiro .p7b para se fundir.

- E se o certificado emitido estiver em estado de incapacidade no portal Azure?

     Consulte o separador **Operação certificado** para rever a mensagem de erro desse certificado.

- E se eu vir **Error type 'O nome do sujeito fornecido não é um nome X500 válido'**?

     Este erro pode ocorrer se **o Nome do Sujeito** incluir caracteres especiais. Consulte as notas no portal Azure e instruções PowerShell.

---

## <a name="next-steps"></a>Passos seguintes

- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
- [Referência Azure Key Vault REST API](/rest/api/keyvault)
- [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate)
- [Cofres - Política de Acesso de Atualização](/rest/api/keyvault/vaults/updateaccesspolicy)