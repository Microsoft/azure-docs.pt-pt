---
title: Métodos de criação de certificados
description: Maneiras de criar um certificado no Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 63768b83baafe00348a28c5c9c99e5f16619ac99
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815921"
---
# <a name="certificate-creation-methods"></a>Métodos de criação de certificados

 Um certificado Key Vault (KV) pode ser criado ou importado em um cofre de chaves. Quando um certificado KV é criado, a chave privada é criada dentro do cofre de chaves e nunca exposta ao proprietário do certificado. Veja a seguir as maneiras de criar um certificado no Key Vault:  

-   **Criar um certificado autoassinado:** Isso criará um par de chaves pública-privada e o associará a um certificado. O certificado será assinado por sua própria chave.  

-    **Crie um novo certificado manualmente:** Isso criará um par de chaves pública-privada e gerará uma solicitação de assinatura de certificado X. 509. A solicitação de assinatura pode ser assinada pela autoridade de registro ou autoridade de certificação. O certificado X509 assinado pode ser mesclado com o par de chaves pendente para concluir o certificado KV no Key Vault. Embora esse método exija mais etapas, ele fornece maior segurança, pois a chave privada é criada e restrita a Key Vault. Isso é explicado no diagrama a seguir.  

![Criar um certificado com sua própria autoridade de certificação](media/certificate-authority-1.png)  

As descrições a seguir correspondem às etapas de letra verde no diagrama anterior.

1. No diagrama acima, seu aplicativo está criando um certificado que começa internamente criando uma chave no cofre de chaves.
2. Key Vault retorna ao seu aplicativo uma solicitação de assinatura de certificado (CSR)
3. Seu aplicativo passa o CSR para a autoridade de certificação escolhida.
4. A AC escolhida responde com um certificado X509.
5. O aplicativo conclui a criação do novo certificado com uma fusão do certificado X509 de sua autoridade de certificação.

-   **Crie um certificado com um provedor de emissor conhecido:** Esse método exige que você faça uma tarefa única de criar um objeto emissor. Depois que um objeto emissor é criado no cofre de chaves, seu nome pode ser referenciado na política do certificado KV. Uma solicitação para criar esse certificado KV criará um par de chaves no cofre e se comunicará com o serviço do provedor emissor usando as informações no objeto emissor referenciado para obter um certificado X509. O certificado X509 é recuperado do serviço emissor e é mesclado com o par de chaves para concluir a criação do certificado KV.  

![Criar um certificado com uma autoridade de certificação Key Vault parceria](media/certificate-authority-2.png)  

As descrições a seguir correspondem às etapas de letra verde no diagrama anterior.

1. No diagrama acima, seu aplicativo está criando um certificado que começa internamente criando uma chave no cofre de chaves.
2. Key Vault envia e solicitação de certificado SSL para a autoridade de certificação.
3. O aplicativo pesquisa, em um loop e um processo de espera, para sua Key Vault para a conclusão do certificado. A criação do certificado é concluída quando Key Vault recebe a resposta da autoridade de certificação com o certificado X509.
4. A autoridade de certificação responde à solicitação de certificado SSL de Key Vault com um certificado SSL X509.
5. A nova criação de certificado é concluída com a fusão do certificado X509 para a autoridade de certificação.

## <a name="asynchronous-process"></a>Processo assíncrono
A criação de certificado KV é um processo assíncrono. Esta operação criará uma solicitação de certificado KV e retornará um código de status HTTP de 202 (aceito). O status da solicitação pode ser rastreado sondando o objeto pendente criado por essa operação. O URI completo do objeto pendente é retornado no cabeçalho de local.  

Quando uma solicitação para criar um certificado KV for concluída, o status do objeto pendente será alterado para "concluído" em "em andamento" e uma nova versão do certificado KV será criada. Isso se tornará a versão atual.  

## <a name="first-creation"></a>Primeira criação
 Quando um certificado KV é criado pela primeira vez, uma chave endereçável e um segredo também são criados com o mesmo nome do certificado. Se o nome já estiver em uso, a operação falhará com um código de status http 409 (conflito).
A chave endereçável e o segredo obtêm seus atributos dos atributos do certificado KV. A chave endereçável e o segredo criados dessa forma são marcados como chaves e segredos gerenciados, cujo tempo de vida é gerenciado pelo Key Vault. Chaves e segredos gerenciados são somente leitura. Nota: Se um certificado KV expirar ou estiver desabilitado, a chave e o segredo correspondentes ficarão inoperáveis.  

 Se esta for a primeira operação para criar um certificado KV, uma política será necessária.  Uma política também pode ser fornecida com operações de criação sucessivas para substituir o recurso de política. Se uma política não for fornecida, o recurso de política no serviço será usado para criar uma próxima versão do certificado KV. Observe que, embora uma solicitação para criar uma próxima versão esteja em andamento, o certificado KV atual e o segredo e a chave endereçáveis correspondentes permanecem inalterados.  

## <a name="self-issued-certificate"></a>Certificado emitido por conta própria
 Para criar um certificado emitido por conta própria, defina o nome do emissor como "próprio" na política de certificado, conforme mostrado no trecho a seguir da política de certificado.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Se o nome do emissor não for especificado, o nome do emissor será definido como "desconhecido". Quando o emissor for "desconhecido", o proprietário do certificado terá que obter manualmente um certificado X509 do emissor de sua escolha e, em seguida, mesclar o certificado X509 público com o objeto pendente do certificado do cofre de chaves para concluir a criação do certificado.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Provedores de autoridade de certificação parceiros
A criação de certificado pode ser concluída manualmente ou usando um emissor "self". Key Vault também parceiros com determinados provedores de emissor para simplificar a criação de certificados. Os tipos de certificados a seguir podem ser ordenados para o Key Vault com esses provedores de emissor do parceiro.  

|Fornecedor|Tipo de certificado|  
|--------------|----------------------|  
|DigiCert|Key Vault oferece certificados SSL OV ou EV com DigiCert|
|GlobalSign|Key Vault oferece certificados SSL OV ou EV com GlobalSign|

 Um emissor de certificado é uma entidade representada em Azure Key Vault (KV) como um recurso CertificateIssuer. Ele é usado para fornecer informações sobre a origem de um certificado KV; nome do emissor, provedor, credenciais e outros detalhes administrativos.

Observe que quando um pedido é colocado com o provedor emissor, ele pode honrar ou substituir as extensões de certificado X509 e o período de validade do certificado com base no tipo de certificado.  

 Nesse Requer a permissão Certificates/Create.

## <a name="see-also"></a>Consultar Também
 - [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
 - [Monitorizar e gerir a criação do certificados](create-certificate-scenarios.md)
