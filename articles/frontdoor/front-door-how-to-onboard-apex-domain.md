---
title: A bordo de um domínio de raiz ou ápice para uma porta frontal existente - portal Azure
description: Aprenda a embarcar num domínio de raiz ou ápice para uma porta frontal existente utilizando o portal Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 44813a7662420ab4dedcd0bf99cc1eec7e9d9d2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819088"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Integrar um domínio de raiz ou apex no seu Front Door
A Azure Front Door utiliza registos CNAME para validar a propriedade do domínio para o embarque de domínios personalizados. A Porta da Frente não expõe o endereço IP frontend associado ao seu perfil da porta da frente. Por isso, não pode mapear o seu domínio de ápice para um endereço IP se a sua intenção for a bordo da Porta frontal Azure.

O protocolo DNS impede a atribuição de registos CNAME no ápice da zona. Por exemplo, se o seu domínio `contoso.com` for; pode criar registos CNAME `somelabel.contoso.com` para; mas não pode criar CNAME por `contoso.com` si só. Esta restrição apresenta um problema para os proprietários de aplicações que têm aplicações equilibradas por trás da Porta Frontal Azure. Uma vez que a utilização de um perfil da Porta Frontal requer a criação de um registo CNAME, não é possível apontar para o perfil da Porta frontal a partir do ápice da zona.

Este problema pode ser resolvido utilizando registos de pseudónimos em Azure DNS. Ao contrário dos registos da CNAME, os registos de pseudónimos são criados no ápice da zona. Os proprietários de aplicações podem usá-lo para apontar o seu registo de zona para um perfil da Porta frontal que tenha pontos finais públicos. Os proprietários de aplicações apontam para o mesmo perfil da Porta Frontal que é usado para qualquer outro domínio dentro da sua zona de DNS. Por exemplo, `contoso.com` e pode apontar para o mesmo perfil da porta da `www.contoso.com` frente. 

Mapear o seu domínio ápice ou raiz para o seu perfil da porta frontal requer basicamente achatamento CNAME ou perseguição de DNS. Um mecanismo em que o fornecedor de DNS resolve novamente a entrada CNAME até que atinja um endereço IP. Esta funcionalidade é suportada pelo Azure DNS para os pontos finais da Porta Frontal. 

> [!NOTE]
> Existem outros fornecedores de DNS também que suportam o achatado CNAME ou a perseguição de DNS, no entanto, a Azure Front Door recomenda a utilização de DNS Azure para os seus clientes para hospedar os seus domínios.

Pode utilizar o portal Azure para embarcar num domínio ápice na porta da frente e ativar o HTTPS nele associando-o a um certificado de rescisão de TLS. Os domínios apex também são referidos como domínios de raiz ou nus.

## <a name="create-an-alias-record-for-zone-apex"></a>Crie um registo de pseudónimo para o ápice da zona

1. Abra a configuração **Azure DNS** para que o domínio seja a bordo.

1. Criar ou editar o registo para o ápice da zona.

1. Selecione o **tipo** de gravação como registo *A* e, em seguida, selecione *Sim* para o recorde **de Alias .** **O tipo de pseudónimo** deve ser definido para *recurso Azure*.

1. Selecione a subscrição Azure onde o seu perfil da Porta Frontal é hospedado. Em seguida, selecione o recurso Porta Frontal a partir do dropdown **de recursos Azure.**

1. Selecione **OK** para submeter as suas alterações.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Recorde de alias para o ápice da zona":::

1. O passo acima irá criar um registo de zona apex que aponta para o seu recurso Front Door e também um mapeamento de registo CNAME 'afdverify' (exemplo - `afdverify.contosonews.com` ) para que será usado para embarcar o domínio no seu perfil da Porta Frontal.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>A bordo do domínio personalizado na sua Porta da Frente

1. No separador designer da porta frontal, selecione no ícone '+' na secção frontend hosts para adicionar um novo domínio personalizado.

1. Introduza o nome de domínio raiz ou ápice no campo de nome do anfitrião personalizado, exemplo `contosonews.com` .

1. Uma vez validado o mapeamento CNAME do domínio para a porta frontal, selecione para **adicionar** o domínio personalizado.

1. **Selecione Guardar** para submeter as alterações.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Recorde de alias para o ápice da zona":::

## <a name="enable-https-on-your-custom-domain"></a>Ative HTTPS no seu domínio personalizado

1. Selecione o domínio personalizado que foi adicionado e sob a secção **Domínio Personalizado HTTPS,** altere o estado para **Ativado**.

1. Selecione o **tipo de gestão de certificados** para *"Use o meu próprio certificado".*

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="Recorde de alias para o ápice da zona":::    

   > [!WARNING]
   > O tipo de gestão de certificado gerido front door não é suportado atualmente para domínios ápice ou raiz. A única opção disponível para permitir HTTPS num domínio ápice ou raiz para porta frontal é usar o seu certificado TLS/SSL personalizado alojado no Cofre da Chave Azure.

1. Certifique-se de que tem as permissões certas para a Porta frontal aceder ao seu cofre chave, como indicado na UI, antes de avançar para o passo seguinte.

1. Escolha uma **conta Key Vault** a partir da sua subscrição atual e, em seguida, selecione a versão **secreta** e **secreta** apropriada para mapear para o certificado certo.

1. Selecione **Update** para guardar a seleção e, em seguida, selecione **Guardar**.

1. Selecione **Refresh** após alguns minutos e, em seguida, selecione o domínio personalizado novamente para ver o progresso do provisionamento do certificado. 

> [!WARNING]
> Certifique-se de que criou regras de encaminhamento adequadas para o seu domínio ápice ou adicione o domínio às regras de encaminhamento existentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
