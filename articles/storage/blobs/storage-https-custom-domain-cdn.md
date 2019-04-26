---
title: Utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS
description: Saiba como integrar o CDN do Azure com o armazenamento de BLOBs para aceder a blobs com domínios personalizados através de HTTPS
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: 90ddb58f3499180e17df559a98ac8a46b53fb824
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392542"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS

Rede de entrega de conteúdos do Azure (CDN do Azure) suporta agora o HTTPS para nomes de domínio personalizados. Com a CDN do Azure, pode aceder a blobs com o seu nome de domínio personalizado através de HTTPS. Para fazer isso, ativar a CDN do Azure no seu ponto final de web ou de BLOBs e, em seguida, mapeie o CDN do Azure para um nome de domínio personalizado. Depois de terminar, o Azure simplifica a ativar HTTPS para o seu domínio personalizado por meio de acesso de um clique e gestão de certificados completa. Não há nenhum aumento nos preços normais da CDN do Azure.

A CDN do Azure ajuda a proteger a privacidade e integridade dos dados dos seus dados de aplicação web, enquanto estiverem em trânsito. Ao utilizar o protocolo SSL para servir o tráfego através de HTTPS, da CDN do Azure mantém os seus dados encriptados quando são enviados pela internet. Através de HTTPS com a CDN do Azure ajudam a proteger as suas aplicações web contra ataques.

> [!NOTE]  
> Além de fornecer suporte SSL para nomes de domínio personalizados, da CDN do Azure pode ajudá-lo a dimensionar a sua aplicação para fornecer conteúdo de largura de banda alta em todo o mundo. Para obter mais informações, consulte [descrição geral da CDN do Azure](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Início Rápido

Para ativar HTTPS para o ponto de final de armazenamento de BLOBs personalizado, faça o seguinte:

1.  [Integrar uma conta de armazenamento do Azure CDN do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Este artigo explica como criar uma conta de armazenamento no portal do Azure, se ainda não o tiver feito.

    > [!NOTE]  
    > Para adicionar o ponto final de web de armazenamento durante a pré-visualização do suporte de Web sites estáticos no armazenamento do Azure, selecione **origem personalizada** no **tipo de origem** na lista pendente. No portal do Azure, terá de fazê-lo a partir do seu perfil de CDN do Azure em vez de diretamente na sua conta de armazenamento.

2.  [Mapear conteúdo da CDN do Azure para um domínio personalizado](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Ativar o HTTPS num domínio personalizado da CDN do Azure](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Assinaturas de acesso partilhado

Por predefinição, os pontos finais de armazenamento de BLOBs não permitam o acesso de leitura anónimo. Se o ponto final de armazenamento de Blobs está configurado para não permitir o acesso de leitura anónimo, forneça uma [assinatura de acesso partilhado](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) token em cada solicitação de seu domínio personalizado. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

A CDN do Azure não respeita as restrições que são adicionadas para o token de assinatura de acesso partilhado. Por exemplo, todos os tokens de assinatura de acesso partilhado expirarem. Pode continuar a aceder a conteúdo com uma assinatura de acesso partilhado expirados até que os conteúdos são eliminados de nós de extremidade da CDN do Azure. Pode controlar o tempo durante o qual os dados são colocados em cache na CDN do Azure, ao definir o cabeçalho de resposta da cache. Para saber como, veja [gerir a expiração de blobs de armazenamento do Azure na CDN do Azure](../../cdn/cdn-manage-expiration-of-blob-content.md).

Se criar acesso partilhado de duas ou mais URLs de assinatura para o mesmo ponto de final do blob, recomendamos que ativar consulta de cadeia de caracteres colocação em cache na CDN do Azure. Esta ação garante que o Azure trata cada URL como uma entidade exclusiva. Para obter mais informações, consulte [Control Azure CDN caching behavior with query strings](../../cdn/cdn-query-string.md)(Controlar o comportamento de colocação em cache do Azure CDN com cadeias de consulta).

## <a name="http-to-https-redirection"></a>HTTP para redirecionamento a HTTPS

Pode redirecionar o tráfego HTTP para HTTPS. Ao fazer isso requer a utilização do premium de CDN do Azure da Verizon da oferta. [Substituir o comportamento HTTP com o mecanismo de regras do CDN do Azure](../../cdn/cdn-rules-engine.md) ao aplicar a seguinte regra:

![HTTP a regra de redirecionamento de HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*Nome do ponto final da CDN*, que selecionou na lista pendente, refere-se para o nome que configurou para o ponto final da CDN do Azure. *Caminho de origem* refere-se para o caminho na sua conta de armazenamento de origem, onde está armazenado o conteúdo estático. Se estiver hospedando todo o conteúdo estático num único contentor, substitua *caminho de origem* com o nome nesse contentor.

Para se aprofundar nas regras, consulte a [funcionalidades do motor de regras de CDN do Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Preços e faturação

Quando aceder a blobs através da CDN do Azure, paga [os preços de armazenamento de BLOBs](https://azure.microsoft.com/pricing/details/storage/blobs/) para o tráfego entre os nós de extremidade e a origem (armazenamento de BLOBs). Paga [os preços da CDN do Azure](https://azure.microsoft.com/pricing/details/cdn/) para dados que são acedidos a partir de nós de extremidade.

Por exemplo, imaginemos que tem uma conta de armazenamento na região E.U.A. oeste que está a aceder através da CDN do Azure. Quando alguém no Reino Unido tenta acessar um blob nessa conta de armazenamento através da CDN do Azure, o Azure verifica primeiro para o blob no nó de extremidade que esteja mais próximo do Reino Unido. Se o Azure encontrar o blob, ele acessa uma cópia e usa a preços da CDN do Azure, porque a CDN do Azure está a aceder. Se o Azure não encontrar o blob, copia o blob para o nó de extremidade. Esta ação resulta em custos de transação e de saída, conforme especificado nos preços de armazenamento de Blobs. Azure, em seguida, acede ao ficheiro no nó de extremidade, o que resulta numa faturação da CDN do Azure.

Sobre o [CDN do Azure, página de preços](https://azure.microsoft.com/pricing/details/cdn/), o suporte HTTPS para nomes de domínios personalizados está disponível na CDN do Azure apenas a partir de produtos da Verizon Standard e Premium.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar um nome de domínio personalizado para o ponto final de armazenamento de BLOBs](storage-custom-domain-name.md)
* [Alojamento de site estático no Armazenamento do Azure](storage-blob-static-website.md)
