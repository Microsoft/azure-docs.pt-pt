---
title: DNS no Azure Stack | Documentos da Microsoft
description: Através de DNS no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 3a4efb6282a0aca8173403009fd58e9154a91b2b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728955"
---
# <a name="using-dns-in-azure-stack"></a>Através de DNS no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack suporta as seguintes funcionalidades do sistema de nomes de domínio (DNS):

* Resolução de nomes de anfitrião DNS
* Criar e gerir zonas DNS e registos utilizando a API

## <a name="support-for-dns-hostname-resolution"></a>Suporte para a resolução de nome de anfitrião DNS

Pode especificar uma etiqueta de nome de domínio DNS para recursos de IP público. Utiliza o Azure Stack **domainnamelabel.location.cloudapp.azurestack.external** para o nome de etiqueta e o maps-o para o endereço IP público no Azure Stack geridos servidores DNS.

Por exemplo, se criar um recurso IP público com **contoso** como uma etiqueta de nome de domínio na localização do Azure Stack local, o [nome de domínio completamente qualificado (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)  **contoso.local.cloudapp.azurestack.external** é resolvido para o endereço IP público do recurso. Pode utilizar este FQDN para criar um registo CNAME que aponta para o endereço IP público no Azure Stack de domínio personalizado.

Para saber mais sobre a resolução de nomes, veja a [resolução de DNS](../../dns/dns-for-azure-services.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) artigo.

> [!IMPORTANT]
> Cada etiqueta de nome de domínio que cria tem de ser exclusiva em seu respectivo local do Azure Stack.

A captura de ecrã a seguir mostra a **Criar endereço IP público** caixa de diálogo para a criação de um endereço IP público através do portal:

![Criar um endereço IP público](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Cenário de exemplo

Tem um balanceador de carga de processamento de pedidos de um aplicativo web. Por trás da carga balanceador é um web site em execução numa ou mais máquinas virtuais. Pode acessar o site da web com balanceamento de carga com um nome DNS, em vez de um endereço IP.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Criar e gerir zonas DNS e registos utilizando a API

Pode criar e gerir zonas DNS e registos no Azure Stack.

O Azure Stack fornece um serviço DNS semelhante para o Azure, através de APIs que são consistentes com as APIs de DNS do Azure.  Ao alojar os seus domínios no DNS do Azure Stack, pode gerir os registos DNS com as mesmas credenciais, APIs e ferramentas. Também pode utilizar a mesma faturação e suporte dos outros serviços do Azure.

A infra-estrutura de DNS do Azure Stack é mais compacta do que o Azure. O tamanho e a localização de uma implementação do Azure Stack afeta o escopo DNS, dimensionamento e desempenho. Isso também significa que a desempenho, disponibilidade, distribuição global e alta disponibilidade podem variar de implementação para implantação.

## <a name="comparison-with-azure-dns"></a>Comparação com o DNS do Azure

DNS no Azure Stack é semelhante ao DNS no Azure, mas existem algumas exceções importantes:

* **Não oferece suporte a registros AAAA**: O Azure Stack não suporta a registros AAAA porque o Azure Stack não suporta endereços IPv6. Esta é a principal diferença entre o DNS no Azure e o Azure Stack.

* **Não é multi-inquilino**: O serviço de DNS no Azure Stack não é a multi-inquilino. Cada inquilino não é possível criar a mesma zona DNS. Apenas a primeira assinatura tenta criar a zona for concluída com êxito e os pedidos subsequentes irão falhar. Esta é a principal diferença entre o Azure e o DNS do Azure Stack.

* **As etiquetas, metadados e Etags**: Existem pequenas diferenças na forma como o Azure Stack processa etiquetas, metadados, Etags e limites.

Para saber mais sobre o DNS do Azure, veja [zonas e registos DNS](../../dns/dns-zones-records.md).

### <a name="tags"></a>Etiquetas

O DNS do Azure Stack oferece suporte a utilizar etiquetas do Azure Resource Manager nos recursos de zona DNS. Não suporta etiquetas em conjuntos de registos de DNS, embora, como alternativa, **metadados** é suportado em conjuntos de registos de DNS, conforme explicado na próxima seção.

### <a name="metadata"></a>Metadados

Como alternativa às marcas de conjunto de registos, DNS do Azure Stack suporta conjuntos de registos através de anotar *metadados*. Semelhante às etiquetas, metadados permite-lhe associar pares nome-valor de cada conjunto de registos. Por exemplo, isso pode ser útil para registrar a finalidade de cada conjunto de registos. Ao contrário das etiquetas, não é possível utilizar metadados para fornecer uma vista filtrada da fatura do Azure e metadados não não possível especificar numa política do Azure Resource Manager.

### <a name="etags"></a>Etags

Suponha que duas pessoas ou dois processos tentam modificar um registo DNS ao mesmo tempo. Qual vence? E o vencedor sabe que eles já substituídos alterações criadas por outra pessoa?

Utiliza o DNS do Azure Stack *Etags* com segurança lidar com alterações simultâneas para o mesmo recurso. Etags são diferentes do Azure Resource Manager *etiquetas*. Cada recurso DNS (zona ou conjunto de registos) tem uma Etag associada a ele. Quando um recurso é recuperado, o respetivo Etag também é recuperado. Ao atualizar um recurso, pode optar por transmitir volta a Etag para que o DNS do Azure Stack pode verificar se a Etag as correspondências de servidor. Uma vez que cada atualização a um recurso resulta em Etag estão a ser regenerada, um erro de correspondência de Etag indica que tiver ocorrido uma alteração em simultâneo. Etags pode também ser utilizadas ao criar um novo recurso para se certificar de que o recurso ainda não existir.

Por predefinição, os cmdlets do PowerShell de DNS do Azure Stack utilizar Etags para bloquear as alterações em simultâneo a zonas e conjuntos de registos. Pode usar o opcional `-Overwrite` mudar para suprimir as verificações de Etag, que faz com que quaisquer alterações simultâneas que ocorreram sejam substituídas.

Ao nível da API de REST de DNS do Azure Stack, Etags são especificadas usando os cabeçalhos HTTP. O comportamento é descrito na tabela a seguir:

| Cabeçalho | Comportamento|
|--------|---------|
| Nenhuma   | PUT sempre for concluída com êxito (sem verificações de Etag)|
| If-match| PUT apenas é bem-sucedida se o recurso existe e Etag corresponder à|
| If-match *| PUT apenas é bem-sucedida se o recurso existe|
| IF-none-match *| PUT apenas é bem-sucedida se o recurso não existe|

### <a name="limits"></a>Limites

Os limites predefinidos seguintes aplicam-se ao utilizar o DNS do Azure Stack:

| Recurso| Limite predefinido|
|---------|--------------|
| Zonas por subscrição| 100|
| Conjuntos de registos por zona| 5000|
| Registos por conjunto de registos| 20|

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao iDNS para o Azure Stack](azure-stack-understanding-dns.md)
