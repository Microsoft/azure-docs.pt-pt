---
title: A bordo de um domínio de raiz ou ápice para uma porta frontal existente - portal Azure
description: Aprenda a embarcar num domínio de raiz ou ápice para uma porta frontal existente utilizando o portal Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: duau
ms.openlocfilehash: 05267ad43f6e7f89ec50b1765d2475a02fae1702
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399590"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Integrar um domínio de raiz ou apex no seu Front Door
A Azure Front Door utiliza registos CNAME para validar a propriedade do domínio para o embarque de domínios personalizados. Além disso, a Porta Frontal não expõe o endereço IP frontend associado ao seu perfil da Porta frontal e por isso não pode mapear o seu domínio ápice para um endereço IP, se a intenção for a bordo para a Porta frontal Azure.

O protocolo DNS impede a atribuição de registos CNAME no ápice da zona. Por exemplo, se o seu domínio `contoso.com` for; pode criar registos CNAME `somelabel.contoso.com` para; mas não pode criar CNAME por `contoso.com` si só. Esta restrição apresenta um problema para os proprietários de aplicações que têm aplicações equilibradas por trás da Porta Frontal Azure. Uma vez que a utilização de um perfil da Porta Frontal requer a criação de um registo CNAME, não é possível apontar para o perfil da Porta frontal a partir do ápice da zona.

Este problema é resolvido usando registos de pseudónimos no Azure DNS. Ao contrário dos registos da CNAME, os registos de pseudónimos são criados no ápice da zona e os proprietários de aplicações podem usá-lo para apontar o seu registo de apex de zona para um perfil da Porta Frontal que tem pontos finais públicos. Os proprietários de aplicações apontam para o mesmo perfil da Porta Frontal que é usado para qualquer outro domínio dentro da sua zona de DNS. Por exemplo, `contoso.com` e pode apontar para o mesmo perfil da porta da `www.contoso.com` frente. 

Mapear o seu domínio ápice ou raiz para o seu perfil da Porta Frontal requer basicamente achatamento CNAME ou perseguição de DNS, que é um mecanismo onde no fornecedor DNS resolve novamente a entrada CNAME até atingir um endereço IP. Esta funcionalidade é suportada pelo Azure DNS para os pontos finais da Porta Frontal. 

> [!NOTE]
> Existem outros fornecedores de DNS também que suportam o achatado CNAME ou a perseguição de DNS, no entanto, a Azure Front Door recomenda a utilização de DNS Azure para os seus clientes para hospedar os seus domínios.

Pode utilizar o portal Azure para embarcar num domínio ápice na porta da frente e ativar o HTTPS nele associando-o a um certificado de rescisão de TLS. Os domínios apex também são referidos como domínios de raiz ou nus.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Crie um registo de pseudónimo que aponta para o seu perfil da Porta da Frente
> * Adicione o domínio raiz à porta da frente
> * Configurar HTTPS no domínio raiz

> [!NOTE]
> Este tutorial requer que já tenha um perfil da Porta da Frente criado. Consulte outros tutoriais como [Quickstart: Crie uma porta frontal](./quickstart-create-front-door.md) ou crie uma porta frontal com HTTP para [reorientação HTTPS](./front-door-how-to-redirect-https.md) para começar.

## <a name="create-an-alias-record-for-zone-apex"></a>Crie um registo de pseudónimo para o ápice da zona

1. Abra a configuração **Azure DNS** para que o domínio seja a bordo.
2. Criar ou editar o registo para o ápice da zona.
3. Selecione o **tipo** de gravação como registo _A_ e, em seguida, selecione _Sim_ para o recorde **de Alias .** **O tipo de pseudónimo** deve ser definido para _recurso Azure_.
4. Escolha a subscrição Azure onde o seu perfil da Porta Frontal está hospedado e, em seguida, selecione o recurso Porta Frontal a partir do dropdown de **recursos Azure.**
5. Clique **em OK** para submeter as suas alterações.

    ![Recorde de alias para o ápice da zona](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. O passo acima irá criar um registo de zona apex que aponta para o seu recurso Front Door e também um mapeamento de registo CNAME 'afdverify' (exemplo - `afdverify.contosonews.com` ) para o qual será utilizado para a bordo do domínio no seu perfil da Porta `afdverify.<name>.azurefd.net` Frontal.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>A bordo do domínio personalizado na sua Porta da Frente

1. No separador designer da Porta Frontal, clique no ícone '+' na secção frontend hosts para adicionar um novo domínio personalizado.
2. Introduza o nome de domínio raiz ou ápice no campo de nome do anfitrião personalizado, exemplo `contosonews.com` .
3. Uma vez validado o mapeamento CNAME do domínio para a porta frontal, clique em **Adicionar** o domínio personalizado.
4. Clique em **Guardar** para submeter as alterações.

![Menu de domínio personalizado](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Ative HTTPS no seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e na secção **Domínio personalizado HTTPS,** altere o estado de **Ativação**.
2. Selecione o **tipo de gestão de certificados** para _"Use o meu próprio certificado"._

> [!WARNING]
> O tipo de gestão de certificado gerido front door não é suportado atualmente para domínios ápice ou raiz. A única opção disponível para permitir HTTPS num domínio ápice ou raiz para porta frontal é usar o seu certificado TLS/SSL personalizado alojado no Cofre da Chave Azure.

3. Certifique-se de que tem as permissões certas para a Porta frontal aceder ao seu cofre chave, como indicado na UI, antes de avançar para o passo seguinte.
4. Escolha uma **conta Key Vault** a partir da sua subscrição atual e, em seguida, selecione a versão **secreta** e **secreta** apropriada para mapear para o certificado certo.
5. Clique em **Update** para guardar a seleção e, em seguida, clique em **Guardar**.
6. Clique em **Refresh** após alguns minutos e, em seguida, clique novamente no domínio personalizado para ver o progresso do provisionamento do certificado. 

> [!WARNING]
> Certifique-se de que criou regras de encaminhamento adequadas para o seu domínio ápice ou adicione o domínio às regras de encaminhamento existentes.

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).