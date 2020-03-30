---
title: A bordo de um domínio raiz ou ápice para uma porta da frente existente - portal Azure
description: Aprenda a embarcar num domínio de raiz ou ápice para uma porta frontal existente utilizando o portal Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184617"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>A bordo de um domínio de raiz ou ápice na sua porta da frente
A Porta Frontal Azure utiliza registos CNAME para validar a propriedade do domínio para o embarque de domínios personalizados. Além disso, a Porta frontal não expõe o endereço IP frontal associado ao seu perfil frontdoor e por isso não pode mapear o seu domínio ápice para um endereço IP, se a intenção é embarcar na Porta da Frente Azure.

O protocolo DNS impede a atribuição de registos CNAME no ápice da zona. Por exemplo, se `contoso.com`o seu domínio for; pode criar registos `somelabel.contoso.com`CNAME para; mas não se pode criar `contoso.com` CNAME para si mesmo. Esta restrição apresenta um problema para os proprietários de aplicações que têm aplicações equilibradas por detrás da Porta frontal do Azure. Uma vez que usar um perfil front door requer a criação de um disco CNAME, não é possível apontar para o perfil da Porta da Frente a partir do ápice da zona.

Este problema é resolvido usando registos de pseudónimos no DNS Azure. Ao contrário dos registos CNAME, os registos de pseudónimos são criados na zona ápice e os proprietários de aplicações podem usá-lo para apontar o seu registo ápice de zona para um perfil front door que tem pontos finais públicos. Os proprietários de aplicações apontam para o mesmo perfil da Porta Da Frente que é usado para qualquer outro domínio dentro da sua zona DNS. Por `contoso.com` exemplo, `www.contoso.com` e pode apontar para o mesmo perfil da Porta da Frente. 

Mapear o seu ápice ou domínio radicular para o seu perfil front door basicamente requer achatamento cname ou perseguição dNS, que é um mecanismo em que no fornecedor DNS resolve recursivamente a entrada CNAME até atingir um endereço IP. Esta funcionalidade é suportada pelo Azure DNS para os pontos finais da Porta Frontal. 

> [!NOTE]
> Existem também outros fornecedores de DNS que suportam o achatamento de CNAME ou a perseguição de DNS, no entanto, a Azure Front Door recomenda a utilização de DNS Azure para os seus clientes para hospedar os seus domínios.

Pode utilizar o portal Azure para embarcar num domínio ápice na sua Porta Frontal e ativar https nele associando-o a um certificado de rescisão SSL. Os domínios apex também são referidos como domínios raiz ou nu.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Crie um registo de pseudónimos que aponte para o seu perfil da Porta da Frente
> * Adicione o domínio raiz à porta da frente
> * Configurar HTTPS no domínio raiz

> [!NOTE]
> Este tutorial requer que já tenha um perfil front door criado. Consulte outros tutoriais como [Quickstart: Crie uma porta da frente](./quickstart-create-front-door.md) ou [crie uma porta da frente com http para https redirection](./front-door-how-to-redirect-https.md) para começar.

## <a name="create-an-alias-record-for-zone-apex"></a>Crie um recorde de pseudónimo para o ápice da zona

1. Abra a configuração **DNS azure** para que o domínio seja a bordo.
2. Crie ou edite o registo para o ápice da zona.
3. Selecione o **tipo de** disco como _Um_ disco e, em seguida, selecione _Sim_ para o conjunto de **discos Alias**. **O tipo de alias** deve ser definido para o _recurso Azure_.
4. Escolha a subscrição Azure onde o seu perfil Front Door está hospedado e, em seguida, selecione o recurso Porta Frontal a partir da queda de **recursos do Azure.**
5. Clique **em OK** para submeter as suas alterações.

    ![Recorde de pseudónimos para a zona ápice](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. O passo acima irá criar um registo de ápice de zona que aponta para o `afdverify.contosonews.com`seu `afdverify.<name>.azurefd.net` recurso Front Door e também um mapeamento de disco CNAME 'afdcheck' (exemplo - ) para o qual será usado para embarcar no domínio no seu perfil porta da frente.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>A bordo do domínio personalizado na sua porta da frente

1. No separador de design da Porta Frontal, clique no ícone '+' na secção de anfitriões frontend para adicionar um novo domínio personalizado.
2. Introduza o nome de domínio raiz ou apex no campo de nome do anfitrião personalizado, exemplo `contosonews.com`.
3. Assim que o mapeamento CNAME do domínio para a porta da frente for validado, clique em **Adicionar** para adicionar o domínio personalizado.
4. Clique em **Guardar** para submeter as alterações.

![Menu de domínio personalizado](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Ativar HTTPS no seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e sob a secção **Domínio personalizado HTTPS,** altere o estado para **Ativado**.
2. Selecione o **tipo de gestão** do Certificado para _"Utilizar o meu próprio certificado"._

> [!WARNING]
> O tipo de gestão de certificados gerido pela Porta Frontal não é atualmente suportado para domínios ápice ou raiz. A única opção disponível para ativar HTTPS num ápice ou domínio de raiz para porta frontal é usar o seu próprio certificado SSL personalizado hospedado no Cofre de Chaves Azure.

3. Certifique-se de que configura as permissões certas para a Porta Da Frente aceder ao seu cofre chave, como se nota na UI, antes de passar ao passo seguinte.
4. Escolha uma **conta Key Vault** a partir da sua subscrição atual e, em seguida, selecione a versão **Secreta** e **Secreta** apropriada para mapear para o certificado certo.
5. Clique em **Atualizar** para salvar a seleção e, em seguida, clique em **Guardar**.
6. Clique em **Refresh** após alguns minutos e, em seguida, clique no domínio personalizado novamente para ver o progresso do fornecimento de certificados. 

> [!WARNING]
> Certifique-se de que criou regras de encaminhamento adequadas para o seu domínio ápice ou adicionou o domínio às regras de encaminhamento existentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).