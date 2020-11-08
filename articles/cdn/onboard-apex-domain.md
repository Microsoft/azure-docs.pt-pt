---
title: Bordo de um domínio de raiz ou ápice para um ponto final Azure CDN existente - Portal Azure
description: Aprenda a embarcar num domínio de raiz ou ápice para um ponto final Azure CDN existente utilizando o portal Azure.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370352"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Bordo de um domínio de raiz ou ápice para um ponto final Azure CDN existente

A Azure CDN utiliza registos CNAME para validar a propriedade do domínio para o embarque de domínios personalizados. A CDN não expõe o endereço IP frontend associado ao seu perfil CDN. Não é possível mapear o seu domínio de ápice para um endereço IP se a sua intenção for a bordo do Azure CDN.

O protocolo DNS impede a atribuição de registos CNAME no ápice da zona. Por exemplo, se o seu domínio `contoso.com` for; pode criar registos CNAME `somelabel.contoso.com` para; mas não pode criar CNAME por `contoso.com` si só. Esta restrição apresenta um problema para os proprietários de aplicações que têm aplicações equilibradas por trás da Azure CDN. Uma vez que a utilização de um perfil CDN requer a criação de um registo CNAME, não é possível apontar para o perfil cdn do ápice da zona.

Este problema pode ser resolvido utilizando registos de pseudónimos em Azure DNS. Ao contrário dos registos da CNAME, os registos de pseudónimos são criados no ápice da zona. Os proprietários de aplicações podem usá-lo para apontar o seu registo de zona para um perfil CDN que tenha pontos finais públicos. Os proprietários de aplicações apontam para o mesmo perfil CDN que é usado para qualquer outro domínio dentro da sua zona de DNS. Por exemplo, `contoso.com` e pode apontar para o mesmo perfil `www.contoso.com` CDN. 

Mapear o seu domínio ápice ou raiz para o seu perfil CDN requer achatamento CNAME ou perseguição de DNS. Um mecanismo em que o fornecedor de DNS resolve novamente a entrada CNAME até que atinja um endereço IP. Esta funcionalidade é suportada pelo Azure DNS para os pontos finais do CDN. 

> [!NOTE]
> Existem outros fornecedores de DNS que suportam o achatamento cname ou perseguição de DNS, no entanto, a Azure CDN recomenda a utilização de DNS Azure para os seus clientes para hospedar os seus domínios.

Pode utilizar o portal Azure para embarcar num domínio ápice no seu CDN e ativar o HTTPS nele associando-o a um certificado de rescisão de TLS. Os domínios apex também são referidos como domínios de raiz ou nus.

## <a name="create-an-alias-record-for-zone-apex"></a>Crie um registo de pseudónimo para o ápice da zona

1. Abra a configuração **Azure DNS** para que o domínio seja a bordo.

2. Selecione **+ Conjunto de registos**.

3. In **Adicionar recorde,** insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ------|
    | Nome | Insira **@** . |
    | Tipo | **Selecione A**. |
    | Conjunto de registos de alias | Selecione **Sim**. |
    | Tipo de alias | Selecione **recurso Azure**. |
    | Escolha uma subscrição | Selecione a sua subscrição. |
    | Recurso do Azure | Selecione o seu ponto de terminação CDN. |

4. Insira o seu valor para **TTL**.

5. Selecione **OK** para submeter as suas alterações.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Recorde de alias para o ápice da zona":::

6. O passo acima irá criar um registo de zona apex apontando para o seu recurso CDN. Um **cdnverify** de mapeamento de discos CNAME é utilizado para embarcar o domínio no seu perfil CDN.
    1. Exemplo, **cdnverify.contoso.com.**
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>A bordo do domínio personalizado no seu CDN

Depois de registar o domínio personalizado, pode, então, adicioná-lo ao ponto final da CDN. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/) e navegue para o perfil da CDN que contém o ponto final que pretende mapear para um domínio personalizado.
    
2. Na página **perfil da CDN** , selecione o ponto final da CDN que vai ser associado ao domínio personalizado.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="Seleção de pontos finais CDN" border="true":::
    
3. Selecione **+ domínio personalizado**. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Adicionar botão de domínio personalizado" border="true":::

4. In **Add a custom domain** , **Endpoint hostname** , é pré-preenchido e é derivado do seu URL de ponto final CDN: **\<endpoint-hostname>** .azureedge.net. Não pode ser alterado.

5. Para **o nome de anfitrião personalizado,** insira o seu domínio de raiz ou ápice personalizado para utilizar como domínio de origem do seu registo CNAME. 
    1. Por exemplo, **contoso.com**. **Não utilize o nome de subdomínio cdnver.**

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Adicionar domínio personalizado" border="true":::

6. Selecione **Add** (Adicionar).

   O Azure verifica se o registo CNAME existe para o nome de domínio personalizado que introduziu. Se o CNAME estiver correto, o seu domínio personalizado vai ser validado. 

   Pode demorar algum tempo para que as novas definições de domínios personalizados se propaguem a todos os nós periféricos da CDN: 
    - Para os perfis **CDN do Azure Standard da Microsoft** , a propagação normalmente fica concluída em 10 minutos. 
    - Para os perfis **CDN do Azure Standard da Akamai** , a propagação normalmente fica concluída num minuto. 
    - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon** , a propagação normalmente fica concluída em 10 minutos.   

## <a name="enable-https-on-your-custom-domain"></a>Ative HTTPS no seu domínio personalizado

Para obter mais informações sobre como ativar HTTPS no seu domínio personalizado para Azure CDN, consulte [Tutorial: Configurar HTTPS num domínio personalizado Azure CDN](cdn-custom-ssl.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um ponto final CDN](cdn-create-new-endpoint.md).
