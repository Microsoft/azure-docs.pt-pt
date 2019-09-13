---
title: Introdução aos certificados Key Vault
description: Os cenários a seguir descrevem vários dos usos principais do serviço de gerenciamento de certificados de Key Vault, incluindo as etapas adicionais necessárias para criar seu primeiro certificado no cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 338619a13ec3f5fcd0d4fd62cf387f955c556a7c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879301"
---
# <a name="get-started-with-key-vault-certificates"></a>Introdução aos certificados Key Vault
Os cenários a seguir descrevem vários dos usos principais do serviço de gerenciamento de certificados de Key Vault, incluindo as etapas adicionais necessárias para criar seu primeiro certificado no cofre de chaves.

Os itens a seguir são descritos:
- Criando seu primeiro certificado de Key Vault
- Criando um certificado com uma autoridade de certificação que é parceira com Key Vault
- Criando um certificado com uma autoridade de certificação que não é parceira com Key Vault
- Importar um certificado

## <a name="certificates-are-complex-objects"></a>Os certificados são objetos complexos
Os certificados são compostos por três recursos inter-relacionados vinculados como um certificado de Key Vault; metadados de certificado, uma chave e um segredo.


![Os certificados são complexos](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Criando seu primeiro certificado de Key Vault  
 Antes que um certificado possa ser criado em um Key Vault (KV), as etapas de pré-requisito 1 e 2 devem ser realizadas com êxito e um cofre de chaves deve existir para este usuário/organização.  

**Etapa 1** -provedores de autoridade de certificação (CA)  
-   Integração como administrador de ti, administrador PKI ou qualquer pessoa que gerencia contas com CAs, para uma determinada empresa (por exemplo, Contoso) é um pré-requisito para usar Key Vault certificados.  
    As seguintes CAs são os provedores parceiros atuais com Key Vault:  
    -   DigiCert-Key Vault oferece certificados SSL OV com DigiCert.  
    -   GlobalSign-Key Vault oferece certificados SSL OV com GlobalSign.  

**Etapa 2** -um administrador de conta para um provedor de autoridade de certificação cria credenciais a serem usadas pelo Key Vault para registrar, renovar e usar certificados SSL via Key Vault.

**Etapa 3** -um administrador da Contoso, junto com um funcionário da contoso (Key Vault usuário) que possui certificados, dependendo da autoridade de certificação, pode obter um certificado do administrador ou diretamente da conta com a autoridade de certificação.  

- Inicie uma operação de adição de credencial em um cofre de chaves [definindo um recurso de emissor de certificado](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) . Um emissor de certificado é uma entidade representada em Azure Key Vault (KV) como um recurso CertificateIssuer. Ele é usado para fornecer informações sobre a origem de um certificado KV; nome do emissor, provedor, credenciais e outros detalhes administrativos.
  - Ex. MyDigiCertIssuer  
    -   Fornecedor  
    -   Credenciais – credenciais da conta de CA. Cada CA tem seus próprios dados específicos.  

    Para obter mais informações sobre como criar contas com provedores de autoridade de certificação, consulte a postagem relacionada no [blog de Key Vault](https://aka.ms/kvcertsblog).  

**Etapa 3,1** -configurar os [contatos de certificado](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) para notificações. Este é o contato para o usuário Key Vault. Key Vault não impõe essa etapa.  

Observação-esse processo, por meio da etapa 3,1, é uma operação de OneTime.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Criando um certificado com uma autoridade de certificação parceira com Key Vault

![Criar um certificado com uma autoridade de certificação Key Vault parceria](media/certificate-authority-2.png)

**Etapa 4** -as descrições a seguir correspondem às etapas numeradas verdes no diagrama anterior.  
  (1)-no diagrama acima, seu aplicativo está criando um certificado que começa internamente criando uma chave no cofre de chaves.  
  (2)-Key Vault envia uma solicitação de certificado SSL para a autoridade de certificação.  
  (3)-seu aplicativo pesquisa, em um loop e espera processo, para sua Key Vault para a conclusão do certificado. A criação do certificado é concluída quando Key Vault recebe a resposta da autoridade de certificação com o certificado X509.  
  (4)-a autoridade de certificação responde à solicitação de certificado SSL de Key Vault com um certificado SSL X509.  
  (5)-a nova criação de certificado é concluída com a fusão do certificado X509 para a autoridade de certificação.  

  Key Vault usuário – cria um certificado especificando uma política

  -   Repetir conforme necessário  
  -   Restrições de política  
      -   Propriedades X509  
      -   Propriedades da chave  
      -   Referência do provedor-> ex. MyDigiCertIssure  
      -   Informações de renovação-> ex. 90 dias antes da expiração  

  - Um processo de criação de certificado geralmente é um processo assíncrono e envolve a sondagem do cofre de chaves para o estado da operação criar certificado.  
[Operação de obtenção de certificado](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Status: concluído, falha com informações de erro ou cancelado  
      -   Devido ao atraso a ser criado, uma operação de cancelamento pode ser iniciada. O cancelamento pode ou não ser eficaz.  

## <a name="import-a-certificate"></a>Importar um certificado  
 Como alternativa – um certificado pode ser importado para Key Vault – PFX ou PEM.  

 Para obter mais informações sobre o formato PEM, consulte a seção certificados de [sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md).  

 Importar certificado – requer que um PEM ou PFX esteja no disco e tenha uma chave privada. 
-   Você deve especificar: nome do cofre e nome do certificado (a política é opcional)

-   Os arquivos PEM/PFX contêm atributos que o KV pode analisar e usar para popular a política de certificado. Se uma política de certificado já estiver especificada, o KV tentará corresponder os dados do arquivo PFX/PEM.  

-   Depois que a importação for final, as operações subsequentes usarão a nova política (novas versões).  

-   Se não houver nenhuma outra operação, a primeira coisa que o Key Vault faz é enviar um aviso de expiração. 

-   Além disso, o usuário pode editar a política, que é funcional no momento da importação, mas contém padrões em que nenhuma informação foi especificada na importação. Ex. nenhuma informação do emissor  

### <a name="formats-of-import-we-support"></a>Formatos de importação para os quais damos suporte
Damos suporte ao seguinte tipo de importação para o formato de arquivo PEM. Um único certificado codificado por PEM junto com uma chave codificada e não criptografada PKCS # 8 que tem o seguinte

-----INICIAR CERTIFICADO----------FIM DO CERTIFICADO-----

-----INICIAR CHAVE PRIVADA----------ENCERRAR CHAVE PRIVADA-----

Na mesclagem de certificados, damos suporte a dois formatos baseados em PEM. Você pode mesclar um único certificado codificado PKCS # 8 ou um arquivo P7B codificado em base64. -----INICIAR CERTIFICADO----------FIM DO CERTIFICADO-----

No momento, não há suporte para chaves do EC no formato PEM.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Criando um certificado com uma AC não parceira com Key Vault  
 Esse método permite trabalhar com outras CAs do que os provedores parceiros de Key Vault, o que significa que sua organização pode trabalhar com uma CA de sua escolha.  

![Criar um certificado com sua própria autoridade de certificação](media/certificate-authority-1.png)  

 As descrições de etapa a seguir correspondem às etapas de letra verde no diagrama anterior.  

  (1)-no diagrama acima, seu aplicativo está criando um certificado, que é iniciado internamente pela criação de uma chave no cofre de chaves.  

  (2)-Key Vault retorna ao seu aplicativo uma solicitação de assinatura de certificado (CSR).  

  (3)-seu aplicativo passa o CSR para a autoridade de certificação escolhida.  

  (4)-a autoridade de certificação escolhida responde com um certificado X509.  

  (5)-o aplicativo conclui a criação do novo certificado com uma fusão do certificado X509 de sua autoridade de certificação.

## <a name="see-also"></a>Consultar Também

- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
