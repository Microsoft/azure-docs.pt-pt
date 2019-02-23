---
title: Métodos de criação de certificados
description: Formas de criar um certificado no Cofre de chaves.
services: key-vault
documentationcenter: ''
author: bryanla
manager: barbkess
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: bryanla
ms.openlocfilehash: f0f18347c5a297b0f15478c3a96a126c1036f8b1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737421"
---
# <a name="certificate-creation-methods"></a>Métodos de criação de certificados

 Um certificado do Key Vault (KV) pode ser criado ou importado para um cofre de chaves. Quando é criado um certificado de KV a chave privada é criada no interior do Cofre de chaves e nunca é exposta ao proprietário do certificado. Seguem-se formas de criar um certificado no Cofre de chaves:  

-   **Crie um certificado autoassinado:** Isto irá criar um par de chaves públicas-privadas e associá-la com um certificado. O certificado será assinado pela sua própria chave.  

-    **Crie manualmente um novo certificado:** Isto irá criar um par de chaves públicas-privadas e gerar um pedido de assinatura de certificado X.509. O pedido de assinatura pode ser assinado pela sua autoridade de registo ou de uma autoridade de certificação. Emparelhe x509 assinado certificado pode ser intercalado com a chave de pendente para concluir o certificado de KV no Cofre de chaves. Embora este método requer mais passos, ele fornecer com maior segurança como a chave privada é criada no e restrito ao Cofre de chaves. Isso é explicado no diagrama abaixo.  

![Criar um certificado com a sua própria autoridade de certificação](media/certificate-authority-1.png)  

As seguintes descrições correspondem aos passos letras verde no diagrama anterior.

1. No diagrama acima, a aplicação está a criar um certificado que internamente começa com a criação de uma chave no seu Cofre de chaves.
2. Key Vault retorna para seu aplicativo, um certificado de assinatura do pedido (CSR)
3. Seu aplicativo passa o CSR para a AC de escolhida.
4. Sua escolhida AC responde com uma X509 certificado.
5. A aplicação concluir a criação de certificado novo com uma fusão de X509 certificado da AC.

-   **Crie um certificado com um fornecedor de emissor conhecidos:** Este método requer que faça uma única tarefa de criação de um objeto de emissor. Assim que for criado um objeto de emissor no seu Cofre de chaves, o respetivo nome pode ser referenciado na política do certificado KV. Um pedido para criar um certificado de KV irá criar um par de chaves no cofre e comunicar com o serviço do fornecedor de emissor utilizando as informações no objeto referenciado do emissor para obter uma x509 certificado. O certificado é obtido a partir do serviço de emissor e é mesclado com o par de chaves para concluir o KV certificado x509 criação.  

![Criar um certificado com uma autoridade de certificação de uma parceria do Key Vault](media/certificate-authority-2.png)  

As seguintes descrições correspondem aos passos letras verde no diagrama anterior.

1. No diagrama acima, a aplicação está a criar um certificado que internamente começa com a criação de uma chave no seu Cofre de chaves.
2. Envia do Key Vault e solicitação de certificado SSL para a AC.
3. A aplicação consulta, num processo de loop e aguardar, Cofre de chaves para a conclusão de certificado. A criação do certificado está concluída quando o Cofre de chaves recebe a resposta da AC com x509 certificado.
4. A AC responde ao pedido de certificado de SSL do Key Vault com uma X509 certificado SSL.
5. A criação do novo certificado é concluída com a fusão de X509 certificado da AC.

## <a name="asynchronous-process"></a>Processo assíncrono
Criação do certificado KV é um processo assíncrono. Esta operação irá criar um pedido de certificado KV e devolver um código de estado de http de 202 (aceite). O estado do pedido pode ser controlado através de consultas do objeto pendente criado por esta operação. O URI completo do objeto pendente é retornado no cabeçalho LOCATION.  

Quando uma solicitação para criar um certificado de KV for concluída, o estado do objeto pendente será alterado para "concluído" de "inprogress" e será criada uma nova versão do certificado KV. Isto irá tornar-se a versão atual.  

## <a name="first-creation"></a>Criação do primeiro
 Quando um certificado de KV é criado pela primeira vez, uma chave endereçável e o segredo é também criado com o mesmo nome que o certificado. Se o nome já está em utilização, em seguida, a operação falhará com um código de estado de http de 409 (conflito).
A chave endereçável e o segredo obtém seus atributos de atributos de certificado KV. A chave endereçável e o segredo criado dessa forma são marcados como geridas chaves e segredos, cuja vida útil é gerida pelo Cofre de chaves. Gerido chaves e segredos são só de leitura. Nota: Se um certificado de KV expira ou está desabilitado, a chave correspondente e o segredo irão tornar-se inoperáveis.  

 Se esta for a primeira operação para criar um certificado de KV é necessária uma política.  Uma política também pode ser fornecida com sucessivas operações para substituir o recurso de política de criação. Se uma política não for fornecida, em seguida, o recurso de política no serviço é utilizado para criar uma próxima versão do certificado de KV. Tenha em atenção que enquanto um pedido para criar uma próxima versão estiver em curso, o certificado de KV atual e o chave endereçável correspondente e o segredo, permanecem inalterados.  

## <a name="self-issued-certificate"></a>Certificado emitido
 Para criar um certificado emitido, defina o nome do emissor como "Self" na política de certificado, conforme mostrado no seguinte fragmento de política de certificado.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Se o nome do emissor não for especificado, em seguida, o nome do emissor é definido como "Desconhecido". Quando o emissor é "Desconhecido", o proprietário do certificado terá de obter manualmente uma x509 certificado de emissor da sua preferência, em seguida, certificado de intercalação x509 pública com o certificado do Cofre de chaves pendentes objeto para concluir a criação do certificado.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Fornecedores de AC têm uma parceria
Criação do certificado pode ser emissor concluído manualmente ou utilizando um "Self". Key Vault também aos parceiros com outros provedores de emissor para simplificar a criação de certificados. Os seguintes tipos de certificados podem ser encomendados para o key vault com esses provedores de emissor de parceiro.  

|Fornecedor|Tipo de certificado|  
|--------------|----------------------|  
|DigiCert|Key Vault oferece certificados OV ou EV SSL com DigiCert|
|GlobalCert|Key Vault oferece certificados OV ou EV SSL com GlobalSign|

 Um emissor do certificado é uma entidade representada no Cofre de chave de Azure (KV) como um recurso de CertificateIssuer. Ele é usado para fornecer informações sobre a origem de um certificado de KV; nome do emissor, fornecedor, credenciais e outros detalhes administrativos.

Tenha em atenção que, quando uma ordem seja feita com o fornecedor de emissor, podem honrar ou extensões de certificados x509 de substituição e período de validade do certificado com base no tipo de certificado.  

 Autorização: Requer a permissão Criar/certificados.

## <a name="see-also"></a>Consultar Também
 - [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
 - [Monitorizar e gerir a criação do certificados](create-certificate-scenarios.md)
