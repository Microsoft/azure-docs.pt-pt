---
title: Métodos de criação de certificados
description: Saiba mais sobre diferentes opções para criar ou importar um certificado Key Vault em Azure Key Vault. Há várias formas de criar um certificado key vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 72ff2a1a7b8bcff768248833183ce03a169f9a4d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752125"
---
# <a name="certificate-creation-methods"></a>Métodos de criação de certificados

 Um certificado Key Vault (KV) pode ser criado ou importado para um cofre chave. Quando um certificado KV é criado, a chave privada é criada dentro do cofre da chave e nunca exposta ao proprietário do certificado. Seguem-se formas de criar um certificado no Cofre-Chave:  

-   **Criar um certificado auto-assinado:** Isto criará um par de chaves público-privado e associá-lo-á a um certificado. O certificado será assinado pela sua própria chave.  

-    **Criar um novo certificado manualmente:** Isto criará um par de chaves público-privado e gerará um pedido de assinatura de certificado X.509. O pedido de assinatura pode ser assinado pela sua autoridade de registo ou autoridade de certificação. O certificado x509 assinado pode ser fundido com o par de chaves pendente para completar o certificado KV no Key Vault. Embora este método exija mais passos, proporciona-lhe uma maior segurança porque a chave privada é criada e restrita ao Key Vault. Isto é explicado no diagrama abaixo.  

![Crie um certificado com a sua própria autoridade de certificados](../media/certificate-authority-1.png)  

As seguintes descrições correspondem aos passos com letras verdes no diagrama anterior.

1. No diagrama anterior, a sua aplicação cria um certificado que começa internamente por criar uma chave no seu cofre de chaves.
2. O Cofre-Chave devolve ao seu pedido um Pedido de Assinatura de Certificado (CSR)
3. A sua aplicação transmite o CSR para a AC escolhida.
4. A sua AC escolhida responde com um Certificado X509.
5. A sua aplicação completa a nova criação de certificado com uma fusão do Certificado X509 da sua AC.

-   **Criar um certificado com um fornecedor de emitentes conhecido:** Este método requer que você faça uma tarefa única de criar um objeto emitente. Uma vez criado um objeto emitente no cofre-chave, o seu nome pode ser referenciado na política do certificado KV. Um pedido para criar um certificado KV criará um par chave no cofre e comunicará com o serviço de emitente usando as informações no objeto emitente referenciado para obter um certificado x509. O certificado x509 é recuperado do serviço emitente e é fundido com o par chave para completar a criação do certificado KV.  

![Criar um certificado com uma autoridade de certificados parceiros key Vault](../media/certificate-authority-2.png)  

As seguintes descrições correspondem aos passos com letras verdes no diagrama anterior.

1. No diagrama anterior, a sua aplicação cria um certificado que começa internamente por criar uma chave no seu cofre de chaves.
2. O Key Vault envia um pedido de certificado TLS/SSL à AC.
3. A sua aplicação consulta o Key Vault, num processo de ciclo e espera, para averiguar a conclusão do certificado. A criação do certificado é concluída quando o Key Vault receber a resposta da AC com o certificado x509.
4. O CA responde ao Pedido de Certificado TLS/SSL da Key Vault com um certificado TLS/SSL X.509.
5. A sua nova criação de certificados completa-se com a fusão do certificado TLS/SSL X.509 para a AC.

## <a name="asynchronous-process"></a>Processo assíncronos
A criação de certificados KV é um processo assíncronos. Esta operação criará um pedido de certificado KV e devolverá um código de estado http de 202 (Aceito). O estado do pedido pode ser rastreado através da sondagem do objeto pendente criado por esta operação. O URI completo do objeto pendente é devolvido no cabeçalho LOCALIZAÇÃO.  

Quando um pedido de criação de um certificado KV estiver concluído, o estado do objeto pendente mudará para "concluído" a partir de "em curso", e será criada uma nova versão do certificado KV. Esta será a versão atual.  

## <a name="first-creation"></a>Primeira criação
 Quando um certificado KV é criado pela primeira vez, uma chave e segredo endereçado também é criado com o mesmo nome que o do certificado. Se o nome já estiver a ser utilizado, a operação falhará com um código de estado http de 409 (conflito).
A chave endereçada e o segredo obtêm os seus atributos a partir dos atributos do certificado KV. A chave e o segredo endereçados criados desta forma são marcados como chaves e segredos geridos, cuja vida é gerida por Key Vault. Chaves e segredos geridos são só para ler. Nota: Se um certificado KV expirar ou for desativado, a chave e segredo correspondente tornar-se-ão inoperáveis.  

 Se esta for a primeira operação para criar um certificado KV, é necessária uma apólice.  Uma política também pode ser fornecida com operações de criação sucessivas para substituir o recurso de política. Se uma apólice não for fornecida, então o recurso de política no serviço é utilizado para criar uma próxima versão do certificado KV. Note que enquanto um pedido de criação de uma próxima versão está em andamento, o certificado KV atual, e a chave e segredo correspondentes endereçados, permanecem inalterados.  

## <a name="self-issued-certificate"></a>Certificado auto-emitido
 Para criar um certificado auto-emitido, deite o nome do emitente como "Self" na política de certificados, tal como mostrado na sequência do corte da política de certificados.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Se o nome do emitente não for especificado, o nome do emitente é definido como "Desconhecido". Quando o emitente for "Desconhecido", o titular do certificado terá de obter manualmente um certificado x509 do emitente da sua escolha, fundindo depois o certificado público x509 com o certificado de cofre chave pendente para completar a criação do certificado.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Fornecedores de CA parceiros
A criação de certificados pode ser concluída manualmente ou utilizando um emitente "Self". A Key Vault também é parceira de certos fornecedores de emitentes para simplificar a criação de certificados. Os seguintes tipos de certificados podem ser encomendados para o cofre-chave com estes fornecedores de emitentes parceiros.  

|Fornecedor|Tipo de certificado|Configuração de configuração  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault oferece certificados OV ou EV SSL com DigiCert| [Guia de Integração](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault oferece certificados OV ou EV SSL com GlobalSign| [Guia de Integração](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Um emitente de certificado é uma entidade representada no Azure Key Vault (KV) como recurso CertificateIssuer. É utilizado para fornecer informações sobre a origem de um certificado KV; nome do emitente, fornecedor, credenciais e outros detalhes administrativos.

Note que quando uma encomenda é feita com o fornecedor de emitentes, pode honrar ou anular as extensões de certificado x509 e o período de validade do certificado com base no tipo de certificado.  

 Autorização: Requer os certificados/criar permissão.

## <a name="see-also"></a>Consulte também

 - Como guiar para criar certificados em Key Vault usando [Portal](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal), [Azure CLI](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-cli), [Azure PowerShell](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-powershell)
 - [Monitorizar e gerir a criação do certificados](create-certificate-scenarios.md)
