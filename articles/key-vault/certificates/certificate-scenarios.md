---
title: Introdução aos certificados do Key Vault
description: Os seguintes cenários descrevem vários dos usos primários do serviço de gestão de certificados key Vault, incluindo os passos adicionais necessários para a criação do seu primeiro certificado no seu cofre-chave.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 06/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 9c1a08161dafa500e9cab2038621c2329cfe6d27
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286889"
---
# <a name="get-started-with-key-vault-certificates"></a>Introdução aos certificados do Key Vault
Os seguintes cenários descrevem vários dos usos primários do serviço de gestão de certificados key Vault, incluindo os passos adicionais necessários para a criação do seu primeiro certificado no seu cofre-chave.

São delineadas as seguintes:
- Criando o seu primeiro certificado Key Vault
- Criação de um certificado com uma Autoridade de Certificados que é parceira da Key Vault
- Criação de um certificado com uma Autoridade de Certificados que não é parceira da Key Vault
- Importar um certificado

## <a name="certificates-are-complex-objects"></a>Certificados são objetos complexos
Os certificados são compostos por três recursos interligados ligados entre si como certificado key vault; metadados certificados, uma chave, e um segredo.


![Os certificados são complexos](../media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Criando o seu primeiro certificado Key Vault  
 Antes de um certificado poder ser criado num Cofre-Chave (KV), os passos 1 e 2 pré-requisitos devem ser realizados com sucesso e deve existir um cofre-chave para este utilizador/organização.  

**Passo 1** - Provedores da Autoridade de Certificados (CA)  
-   Embarque como o Administrador de TI, PKI Admin ou qualquer pessoa que gere contas com CAs, para uma determinada empresa (ex. Contoso) é um pré-requisito para usar certificados Key Vault.  
    Os seguintes CAs são os atuais fornecedores parceiros com o Key Vault. Saiba mais [aqui](./create-certificate.md#partnered-ca-providers)   
    -   DigiCert - Key Vault oferece certificados OV TLS/SSL com DigiCert.  
    -   GlobalSign - Key Vault oferece certificados OV TLS/SSL com a GlobalSign.  

**Passo 2** - Um administrador de conta para um fornecedor de CA cria credenciais para ser usado pelo Key Vault para inscrever, renovar e usar certificados TLS/SSL através do Key Vault.

**Passo 3** - Um administrador contoso, juntamente com um funcionário da Contoso (utilizador do Cofre-Chave) que detém certificados, dependendo da AC, pode obter um certificado da administração ou diretamente da conta com a AC.  

- Inicie uma operação de credencial adicionada a um cofre de [chaves, definindo um recurso emitente de certificado.](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) Um emitente de certificado é uma entidade representada no Azure Key Vault (KV) como recurso CertificateIssuer. É utilizado para fornecer informações sobre a origem de um certificado KV; nome do emitente, fornecedor, credenciais e outros detalhes administrativos.
  - Por exemplo: MyDigiCertIssuer  
    -   Fornecedor  
    -   Credenciais – Credenciais de conta CA. Cada AC tem os seus próprios dados específicos.  

    Para obter mais informações sobre a criação de contas com fornecedores de CA, consulte o post relacionado no [blog Key Vault](/archive/blogs/kv/manage-certificates-via-azure-key-vault).  

**Passo 3.1** - Configurar [os contactos de certificados](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) para notificações. Este é o contacto para o utilizador do Cofre de Chaves. O Cofre-Chave não impõe este passo.  

Nota - Este processo, através do passo 3.1, é uma operação única.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Criação de um certificado com uma AC em parceria com o Key Vault

![Criar um certificado com uma autoridade de certificados parceiros key Vault](../media/certificate-authority-2.png)

**Passo 4** - As seguintes descrições correspondem aos passos numerados verdes no diagrama anterior.  
  (1) - No diagrama acima, a sua aplicação está a criar um certificado que começa internamente por criar uma chave no cofre da chave.  
  (2) - O Cofre-Chave envia um pedido de certificado TLS/SSL à AC.  
  (3) - As suas sondagens de candidatura, em ciclo e processo de espera, para o seu Cofre-Chave para a conclusão do certificado. A criação do certificado é concluída quando o Key Vault receber a resposta da AC com o certificado x509.  
  (4) - A AC responde ao Pedido de Certificado TLS/SSL da Key Vault com um Certificado X509 TLS/SSL.  
  (5) - A sua nova criação de certificados completa-se com a fusão do Certificado X509 para a AC.  

  Utilizador key Vault – cria um certificado especificando uma política

  -   Repita se necessário  
  -   Restrições políticas  
      -   Propriedades X509  
      -   Propriedades principais  
      -   Referência do fornecedor - > ex. MyDigiCertIssure  
      -   Informação de renovação - > ex. 90 dias antes de expirar  

  - Um processo de criação de certificados é geralmente um processo assíncrodo e envolve sondar o seu cofre chave para o estado da operação de certificado de criação.  
[Obter operação de certificado](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Estado: concluído, falhado com informações de erro ou cancelado  
      -   Devido ao atraso para criar, uma operação de cancelamento pode ser iniciada. O cancelamento pode ou não ser eficaz.  

### <a name="network-security-and-access-policies-associated-with-integrated-ca"></a>Políticas de segurança e acesso de rede associadas à AC integrada
O serviço Key Vault envia pedidos para a AC (tráfego de saída). Portanto, é totalmente compatível com cofres-chave ativados por firewall. O Cofre-Chave não partilha políticas de acesso com a AC. A AC deve ser configurada para aceitar os pedidos de assinatura de forma independente. [Guia sobre a integração da CA fidedigna](./how-to-integrate-certificate-authority.md)

## <a name="import-a-certificate"></a>Importar um certificado  
 Alternativamente – um certificado pode ser importado para Key Vault – PFX ou PEM.  

 Certificado de importação – requer que um PEM ou PFX estejam no disco e tenham uma chave privada. 
-   Tem de especificar: nome do cofre e nome do certificado (a política é opcional)

-   Os ficheiros PEM/PFX contêm atributos que o KV pode analisar e usar para preencher a política de certificados. Se uma política de certificado já estiver especificada, a KV tentará combinar dados do ficheiro PFX/PEM.  

-   Uma vez finalda a importação, as operações subsequentes utilizarão a nova política (novas versões).  

-   Se não houver mais operações, a primeira coisa que o Cofre-Chave faz é enviar um aviso de expiração. 

-   Além disso, o utilizador pode editar a política, que é funcional no momento da importação, mas que contém incumprimentos onde nenhuma informação foi especificada na importação. Por exemplo: nenhuma informação emitente  

### <a name="formats-of-import-we-support"></a>Formatos de Importação que apoiamos
O Azure Key Vault suporta ficheiros de certificados .pem e .pfx para importar certificados para o cofre Key.
Apoiamos o seguinte tipo de formato de ficheiro Import for PEM. Um único certificado codificado PEM juntamente com uma chave codificada e desencriptado PKCS#8 que tem a seguinte

CERTIFICADO -----BEGIN----- ---------------

-----BEGIN CHAVE PRIVADA----- -----END-----

Quando está a importar o certificado, tem de se certificar de que a chave está incluída no próprio ficheiro. Se tiver a chave privada separadamente num formato diferente, terá de combinar a chave com o certificado. Algumas autoridades de certificados fornecem certificados em diferentes formatos, pelo que antes de importar o certificado, certifique-se de que estão em formato .pem ou .pfx. 

### <a name="formats-of-merge-csr-we-support"></a>Formatos de Merge CSR que apoiamos
AKV suporta 2 formatos baseados em PEM. Pode fundir um único certificado codificado PKCS#8 ou um P7B codificado base64 (cadeia de certificados assinados pela CA) 

CERTIFICADO -----BEGIN----- ---------------

Atualmente, não suportamos chaves EC em formato PEM.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Criação de um certificado com um CA não associado ao Key Vault  
 Este método permite trabalhar com outros CAs do que os fornecedores parceiros da Key Vault, o que significa que a sua organização pode trabalhar com um CA à sua escolha.  

![Crie um certificado com a sua própria autoridade de certificados](../media/certificate-authority-1.png)  

 As seguintes descrições do passo correspondem aos passos com letras verdes no diagrama anterior.  

  (1) - No diagrama acima, a sua aplicação está a criar um certificado, que começa internamente por criar uma chave no cofre da chave.  

  (2) - O Cofre-Chave devolve ao seu pedido um Pedido de Assinatura de Certificado (CSR).  

  (3) - A sua aplicação transmite a RSE à sua CA escolhida.  

  (4) - A sua AC escolhida responde com um Certificado X509.  

  (5) - A sua aplicação completa a nova criação de certificados com uma fusão do Certificado X509 da sua AC.