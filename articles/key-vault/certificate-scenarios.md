---
title: Começar com certificados key vault
description: Os seguintes cenários descrevem vários dos principais usos do serviço de gestão de certificados da Key Vault, incluindo os passos adicionais necessários para a criação do seu primeiro certificado no seu cofre chave.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 32a453678fe3702fcb4b77f0b04a8ed5c889ef59
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386073"
---
# <a name="get-started-with-key-vault-certificates"></a>Começar com certificados key vault
Os seguintes cenários descrevem vários dos principais usos do serviço de gestão de certificados da Key Vault, incluindo os passos adicionais necessários para a criação do seu primeiro certificado no seu cofre chave.

São delineados os seguintes:
- Criando o seu primeiro certificado key vault
- Criação de um certificado com uma Autoridade de Certificados que é parceira da Key Vault
- Criação de um certificado com uma Autoridade de Certificados que não seja parceira da Key Vault
- Importar um certificado

## <a name="certificates-are-complex-objects"></a>Os certificados são objetos complexos
Os certificados são compostos por três recursos interligados ligados entre si como certificado key vault; metadados de certificados, uma chave, e um segredo.


![Os certificados são complexos](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Criando o seu primeiro certificado key vault  
 Antes de um certificado poder ser criado num Cofre chave (KV), os passos 1 e 2 pré-requisitos devem ser realizados com sucesso e deve existir um cofre chave para este utilizador/organização.  

**Passo 1** - Prestadores da Autoridade de Certificados (CA)  
-   Embarque como administrador de TI, Administrador PKI ou qualquer pessoa que gere contas com CA, para uma determinada empresa (ex. Contoso) é um pré-requisito para a utilização de certificados key vault.  
    Os seguintes CA são os atuais fornecedores parceiros com a Key Vault:  
    -   DigiCert - Key Vault oferece certificados OV TLS/SSL com DigiCert.  
    -   GlobalSign - Key Vault oferece certificados OV TLS/SSL com GlobalSign.  

**Passo 2** - Um administrador de conta para um fornecedor de CA cria credenciais a utilizar pela Key Vault para inscrever, renovar e utilizar certificados TLS/SSL via Key Vault.

**Passo 3** - Um administrador da Contoso, juntamente com um funcionário da Contoso (utilizador do Key Vault) que possua certificados, dependendo da AC, pode obter um certificado do administrador ou diretamente da conta com a AC.  

- Inicie uma operação de credencial adicionada a um cofre [chave, definindo um](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) recurso emissor de certificado. Um emitente de certificado é uma entidade representada no Cofre chave azure (KV) como recurso CertificateIssuer. É utilizado para fornecer informações sobre a origem de um certificado KV; nome emitente, fornecedor, credenciais e outros detalhes administrativos.
  - Ex. MyDigiCertIssuer  
    -   Fornecedor  
    -   Credenciais – Credenciais de conta CA. Cada AC tem os seus próprios dados específicos.  

    Para obter mais informações sobre a criação de contas com fornecedores ca, consulte a publicação relacionada no [blog Key Vault](https://aka.ms/kvcertsblog).  

**Passo 3.1** - Configurar contactos de [certificados](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) para notificações. Este é o contacto para o utilizador do Cofre chave. O Cofre chave não aplica este passo.  

Nota - Este processo, através do passo 3.1, é uma operação única.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Criação de um certificado com uma AC em parceria com o Key Vault

![Criar um certificado com uma autoridade de certificados parceira da Key Vault](media/certificate-authority-2.png)

**Passo 4** - As seguintes descrições correspondem aos passos numerados verdes no diagrama anterior.  
  (1) - No diagrama acima, a sua aplicação está a criar um certificado que começa internamente por criar uma chave no seu cofre chave.  
  (2) - O Cofre-Chave envia um Pedido de Certificado TLS/SSL para a AC.  
  (3) - As suas sondagens de candidatura, num processo de loop e espera, para o seu Cofre Chave para a conclusão do certificado. A criação do certificado fica completa quando a Key Vault recebe a resposta da AC com certificado x509.  
  (4) - A AC responde ao pedido de certificado TLS/SSL da Key Vault com um Certificado X509 TLS/SSL.  
  (5) - A sua nova criação de certificado completa com a fusão do Certificado X509 para a AC.  

  Utilizador chave Vault - cria um certificado especificando uma política

  -   Repita conforme necessário  
  -   Restrições políticas  
      -   Propriedades X509  
      -   Propriedades da chave  
      -   Referência do fornecedor - > ex. MyDigiCertIssure  
      -   Informação de renovação - > ex. 90 dias antes do termo  

  - Um processo de criação de certificadoé geralmente um processo assíncrono e envolve a sondagem do seu cofre chave para o estado da operação de certificado de criação.  
[Obter operação de certificado](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Estado: concluído, falhado com informações de erro ou, cancelado  
      -   Devido ao atraso na criação, pode ser iniciada uma operação de cancelamento. O cancelamento pode ou não ser eficaz.  

## <a name="import-a-certificate"></a>Importar um certificado  
 Alternativamente – um certificado pode ser importado para Key Vault – PFX ou PEM.  

 Para obter mais informações sobre o formato PEM, consulte a secção de certificados de [Sobre chaves, segredos e certificados.](about-keys-secrets-and-certificates.md)  

 Certificado de importação – requer que um PEM ou PFX esteja em disco e tenha uma chave privada. 
-   Deve especificar: nome do cofre e nome do certificado (a política é opcional)

-   Os ficheiros PEM/PFX contêm atributos que o KV pode analisar e usar para preencher a política de certificados. Se uma política de certificado já estiver especificada, a KV tentará comparar os dados do ficheiro PFX/PEM.  

-   Uma vez que a importação seja final, as operações subsequentes utilizarão a nova política (novas versões).  

-   Se não houver mais operações, a primeira coisa que o Cofre chave faz é enviar um aviso de validade. 

-   Além disso, o utilizador pode editar a política, que é funcional no momento da importação, mas contém incumprimentos em que nenhuma informação foi especificada na importação. Ex. nenhuma informação emitente  

### <a name="formats-of-import-we-support"></a>Formatos de Importação que apoiamos
Apoiamos o seguinte tipo de Importação para o formato de ficheiro PEM. Um único certificado codificado pEM juntamente com uma chave codificada e não encriptada PKCS#8 que tem o seguinte

-----CERTIFICADO ----- -----FIM CERTIFICADODEDEDEDEDEDE-----

-----BEGIN CHAVE PRIVADA----- -----CHAVE PRIVADA-----

Na fusão de certificados suportamos 2 formatos baseados em PEM. Pode fundir um único certificado codificado PKCS#8 ou um ficheiro P7B codificado com base64. -----CERTIFICADO ----- -----FIM CERTIFICADODEDEDEDEDEDE-----

Atualmente não suportamos chaves EC em formato PEM.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Criar um certificado com um CA não em parceria com o Key Vault  
 Este método permite trabalhar com outros CAs que não os fornecedores parceiros da Key Vault, o que significa que a sua organização pode trabalhar com um CA à sua escolha.  

![Crie um certificado com a sua própria autoridade de certificados](media/certificate-authority-1.png)  

 As seguintes descrições do passo correspondem aos passos com letras verdes no diagrama anterior.  

  (1) - No diagrama acima, a sua aplicação está a criar um certificado, que começa internamente por criar uma chave no seu cofre chave.  

  (2) - O Cofre-Chave devolve à sua aplicação um Pedido de Assinatura de Certificado (CSR).  

  (3) - A sua candidatura passa a RSE para a sua CA escolhida.  

  (4) - A sua CA escolhida responde com um Certificado X509.  

  (5) - O seu pedido completa a criação do novo certificado com uma fusão do Certificado X509 da sua CA.

## <a name="see-also"></a>Veja Também

- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
