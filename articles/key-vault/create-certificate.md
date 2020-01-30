---
title: Métodos de criação de certificados
description: Formas de criar um certificado no Cofre Chave.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 26309bb9a7b9785dbac7f42b0c20de99bca10a17
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769251"
---
# <a name="certificate-creation-methods"></a>Métodos de criação de certificados

 Um certificado key vault (KV) pode ser criado ou importado para um cofre chave. Quando um certificado KV é criado, a chave privada é criada dentro do cofre chave e nunca exposta ao proprietário do certificado. Seguem-se formas de criar um certificado no Cofre Chave:  

-   **Criar um certificado auto-assinado:** Isto criará um par de chaves público-privado e associá-lo-á a um certificado. O certificado será assinado por sua própria chave.  

-    **Crie um novo certificado manualmente:** Isto criará um par de chaves público-privado e gerará um pedido de assinatura de certificado X.509. A solicitação de assinatura pode ser assinada pela autoridade de registro ou autoridade de certificação. O certificado X509 assinado pode ser mesclado com o par de chaves pendente para concluir o certificado KV no Key Vault. Embora esse método exija mais etapas, ele fornece maior segurança, pois a chave privada é criada e restrita a Key Vault. Isso é explicado no diagrama a seguir.  

![Criar um certificado com sua própria autoridade de certificação](media/certificate-authority-1.png)  

As seguintes descrições correspondem aos passos com letras verdes no diagrama anterior.

1. No diagrama acima, a sua aplicação está a criar um certificado que começa internamente por criar uma chave no seu cofre chave.
2. Key Vault devolve à sua aplicação um Pedido de Assinatura de Certificado (CSR)
3. A sua candidatura passa a RSE para a sua CA escolhida.
4. O seu CA escolhido responde com um Certificado X509.
5. A sua aplicação completa a criação do novo certificado com uma fusão do Certificado X509 da sua CA.

-   **Criar um certificado com um fornecedor de emitente conhecido:** Este método requer que faça uma tarefa única de criar um objeto emitente. Uma vez criado um objeto emitente no seu cofre chave, o seu nome pode ser referenciado na política do certificado KV. Um pedido para criar tal certificado KV criará um par chave no cofre e comunicará com o serviço de emitente fornecedor usando a informação no objeto emissor referenciado para obter um certificado x509. O certificado x509 é recuperado do serviço emitente e é fundido com o par chave para completar a criação do certificado KV.  

![Criar um certificado com uma autoridade de certificação Key Vault parceria](media/certificate-authority-2.png)  

As seguintes descrições correspondem aos passos com letras verdes no diagrama anterior.

1. No diagrama acima, a sua aplicação está a criar um certificado que começa internamente por criar uma chave no seu cofre chave.
2. O Cofre chave envia um Pedido de Certificado TLS/SSL para a AC.
3. As suas sondagens de candidatura, num processo de loop e espera, para o seu Cofre Chave para a conclusão do certificado. A criação do certificado fica completa quando a Key Vault recebe a resposta da AC com certificado x509.
4. O CA responde ao Pedido de Certificado TLS/SSL da Key Vault com um certificado TLS/SSL X.509.
5. A sua nova criação de certificado completa com a fusão do certificado TLS/SSL X.509 para a AC.

## <a name="asynchronous-process"></a>Processo assíncrono
A criação de certificados KV é um processo assíncrono. Esta operação criará um pedido de certificado KV e devolverá um código de estado http de 202 (Aceito). O estado do pedido pode ser acompanhado através da sondagem do objeto pendente criado por esta operação. O URI completo do objeto pendente é devolvido no cabeçalho LOCAL.  

Quando um pedido de criação de um certificado KV estiver concluído, o estado do objeto pendente passará para "concluído" a partir de "inprogress", e será criada uma nova versão do certificado KV. Esta será a versão atual.  

## <a name="first-creation"></a>Primeira criação
 Quando um certificado KV é criado pela primeira vez, uma chave e segredo endereçados também é criado com o mesmo nome do certificado. Se o nome já estiver em uso, então a operação falhará com um código de estado de http de 409 (conflito).
A chave endereçada e o segredo obtêm os seus atributos a partir dos atributos do certificado KV. A chave endereçada e o segredo criados desta forma estão marcados como chaves e segredos geridos, cuja vida é gerida pela Key Vault. Chaves e segredos geridos são apenas leitura. Nota: Se um certificado KV expirar ou estiver desativado, a chave e o segredo correspondentes tornar-se-ão inoperáveis.  

 Se esta for a primeira operação para criar um certificado KV, então é necessária uma apólice.  Uma política também pode ser fornecida com operações de criação sucessivas para substituir o recurso político. Se uma política não for fornecida, o recurso de política no serviço é utilizado para criar uma próxima versão do certificado KV. Note que, embora esteja em curso um pedido para criar uma próxima versão, o certificado KV atual, e a chave e segredo correspondentes, permanecem inalterados.  

## <a name="self-issued-certificate"></a>Certificado auto-emitido
 Para criar um certificado auto-emitido, defino o nome emitente como "Self" na política de certificados, como mostrado no seguinte corte da política de certificados.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Se o nome do emitente não for especificado, então o nome do emitente está definido para "Desconhecido". Quando o emitente for "Desconhecido", o proprietário do certificado terá de obter manualmente um certificado x509 do emitente à sua escolha, depois fundir o certificado público x509 com o certificado de cofre chave pendente para completar a criação do certificado.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Fornecedores de CA parceiros
A criação de certificadopode ser concluída manualmente ou utilizando um emitente "Self". A Key Vault também é parceira de certos fornecedores de emitentes para simplificar a criação de certificados. Os seguintes tipos de certificados podem ser encomendados para o cofre chave com estes fornecedores de emitentes parceiros.  

|Fornecedor|Tipo de certificado|  
|--------------|----------------------|  
|DigiCert|Key Vault oferece certificados OV ou EV SSL com DigiCert|
|GlobalSign|Key Vault oferece certificados OV ou EV SSL com GlobalSign|

 Um emissor de certificado é uma entidade representada em Azure Key Vault (KV) como um recurso CertificateIssuer. Ele é usado para fornecer informações sobre a origem de um certificado KV; nome do emissor, provedor, credenciais e outros detalhes administrativos.

Note que quando uma encomenda é feita com o provedor emitente, pode honrar ou anular as extensões do certificado x509 e o período de validade do certificado com base no tipo de certificado.  

 Autorização: Requer os certificados/criar permissão.

## <a name="see-also"></a>Veja também
 - [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
 - [Monitorizar e gerir a criação do certificados](create-certificate-scenarios.md)
