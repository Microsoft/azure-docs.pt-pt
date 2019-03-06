---
title: Comece com certificados do Key Vault
description: Os cenários seguintes realçam vários das utilizações de principal de serviço de gestão de certificados do Key Vault incluindo os passos adicionais necessários para criar seu primeiro certificado no seu Cofre de chaves.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 73a09a99b1dd275b20bcd389e9cc391f638e8bdd
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404268"
---
# <a name="get-started-with-key-vault-certificates"></a>Comece com certificados do Key Vault
Os cenários seguintes realçam vários das utilizações de principal de serviço de gestão de certificados do Key Vault incluindo os passos adicionais necessários para criar seu primeiro certificado no seu Cofre de chaves.

É descrita a seguir:
- Criando seu primeiro certificado do Key Vault
- Criar um certificado com uma autoridade de certificação que é uma parceria com o Key Vault
- Criar um certificado com uma autoridade de certificação que não é compartilhado com o Key Vault
- Importar um certificado

## <a name="certificates-are-complex-objects"></a>Os certificados são objetos complexos
Certificados são compostas por três recursos inter-relacionados ligados em conjunto como um certificado do Key Vault; metadados de certificado, uma chave e um segredo.


![Os certificados são complexos](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Criando seu primeiro certificado do Key Vault  
 Antes de um certificado pode ser criado num cofre de chave (KV), os passos de pré-requisitos 1 e 2 devem ser realizados com êxito e um cofre de chaves tem de existir para este utilizador / organização.  

**Passo 1** -fornecedores de autoridade de certificado  
-   Introdução como o administrador de TI, o administrador de PKI ou como qualquer pessoa, gerenciamento de contas com o ACS, para uma determinada empresa (ex. A Contoso) é um pré-requisito para utilizar certificados do Key Vault.  
    As ACs seguintes estão os provedores têm uma parceria atuais com o Key Vault:  
    -   DigiCert - Key Vault oferece OV SSL certificados com a DigiCert.  
    -   GlobalSign - Key Vault oferece OV SSL certificados com GlobalSign  
    -   WoSign - Key Vault oferece OV SSL ou certificados SSL de EV com WoSign baseia-se a definição configurada pelo cliente na respetiva conta WoSign no portal do WoSign.  

**Passo 2** -um administrador de conta para um fornecedor de AC cria as credenciais para ser utilizado pelo Key Vault para se inscrever, renovar e utilizar certificados SSL através do Key Vault.

**Passo 3** -administrador A Contoso, juntamente com um funcionário da Contoso (utilizador do Key Vault) que é proprietário de certificados, dependendo da AC, pode obter um certificado do administrador ou diretamente da conta com a AC.  

-   Iniciar uma operação de credenciais do add para um cofre de chaves por [definir um emissor de certificado](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) recursos. Um emissor do certificado é uma entidade representada no Cofre de chave de Azure (KV) como um recurso de CertificateIssuer. Ele é usado para fornecer informações sobre a origem de um certificado de KV; nome do emissor, fornecedor, credenciais e outros detalhes administrativos.
    -   Ex. MyDigiCertIssuer  
        -   Fornecedor  
        -   Credenciais – as credenciais da conta de AC. Cada AC tem seus próprios dados específicos.  

     Para obter mais informações sobre como criar contas com fornecedores de AC, consulte a postagem relacionada sobre o [blogue do Key Vault](https://aka.ms/kvcertsblog).  

**Passo 3.1** -configure [contactos do certificado](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) para notificações. Este é o contacto para o utilizador do Key Vault. Cofre de chaves não impõe a este passo.  

Tenha em atenção - este processo, através do passo 3.1, é uma operação única.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Criação de um certificado com uma AC de uma parceria com o Key Vault

![Criar um certificado com uma autoridade de certificação de uma parceria do Key Vault](media/certificate-authority-2.png)

**Passo 4** -as seguintes descrições correspondem para os passos numerados verde no diagrama anterior.  
  (1) - no diagrama acima, a aplicação está a criar um certificado que internamente começa com a criação de uma chave no seu Cofre de chaves.  
  (2) - Key Vault envia um pedido de certificado de SSL para a AC.  
  (3) - a aplicação consulta, num processo de loop e aguardar, para o Cofre de chave para a conclusão de certificado. A criação do certificado está concluída quando o Cofre de chaves recebe a resposta da AC com x509 certificado.  
  (4) - AC responde ao pedido de certificado de SSL do Key Vault com uma X509 certificado SSL.  
  (5) - a criação do novo certificado é concluída com a fusão de X509 certificado da AC.  

  Utilizador do Key Vault, cria um certificado ao especificar uma política

  -   Repita conforme necessário  
  -   Restrições de política  
      -   X509 propriedades  
      -   Propriedades da chave  
      -   Referência do fornecedor - > ex. MyDigiCertIssure  
      -   Informação de renovação - > ex. 90 dias antes da expiração  

  - Um processo de criação do certificado é, normalmente, um processo assíncrono e envolve o seu Cofre de chaves para o estado da operação de certificado a criação de consulta.  
[Operação de obtenção de certificado](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Estado: concluída, com informações de erro ou falhadas, foi cancelada  
      -   Devido a atraso para criar, pode ser iniciada uma operação de cancelamento. O cancelamento pode ou não estar em vigor.  

## <a name="import-a-certificate"></a>Importar um certificado  
 Em alternativa – um certificado pode ser importado para o Cofre de chaves – PFX ou PEM.  

 Para obter mais informações sobre o formato PEM, consulte a secção de certificados de [sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md).  

 Importar certificado – requer um PEM ou PFX no disco e tem uma chave privada. 
-   Tem de especificar: nome e o nome do certificado do cofre (a política é opcional)

-   PEM / ficheiros PFX contém atributos que KV pode analisar e usar para preencher a política de certificados. Se já for especificada uma política de certificado, KV tentará corresponder dados a partir de PFX / ficheiro PEM.  

-   Assim que a importação estiver final, as operações subsequentes utilizarão a nova política (novas versões).  

-   Se não existirem não existem outras operações, a primeira coisa de que o Cofre de chaves faz é enviar um aviso de expiração. 

-   Além disso, o utilizador pode editar a política, o que está funcional no momento da importação, mas, contém padrões em que nenhuma informação foi especificada na importação. Ex. Não existem informações de emissor  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Criação de um certificado com uma AC não fez uma parceria com o Key Vault  
 Esse método permite trabalhar com outras ACs que Fornecedores têm uma parceria de Key Vault, que significa que a sua organização pode trabalhar com uma autoridade de certificação da sua preferência.  

![Criar um certificado com a sua própria autoridade de certificação](media/certificate-authority-1.png)  

 As descrições de passo seguinte correspondem aos passos letras verde no diagrama anterior.  

  (1) - no diagrama acima, a aplicação está a criar um certificado que internamente começa com a criação de uma chave no seu Cofre de chaves.  

  (2) - Key Vault retorna para seu aplicativo, um certificado de assinatura do pedido (CSR).  

  (3) - seu aplicativo passa o CSR para a AC de escolhida.  

  (4) - sua escolhida AC responde com uma X509 certificado.  

  (5) - a aplicação concluir a criação de certificado novo com uma fusão de X509 certificado da AC.

## <a name="see-also"></a>Consultar Também

- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
