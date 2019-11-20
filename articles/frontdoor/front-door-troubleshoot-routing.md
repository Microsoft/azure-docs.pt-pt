---
title: Solucionar problemas de configuração do serviço de porta frontal do Azure
description: Neste tutorial, você aprenderá a solucionar automaticamente alguns dos problemas comuns que você pode enfrentar na sua porta de frente.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: c0d6303620b92368e422b54beab4f9c346d022a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184567"
---
# <a name="troubleshooting-common-routing-issues"></a>Solucionando problemas comuns de roteamento
Este artigo descreve como solucionar alguns dos problemas de roteamento comuns que você pode enfrentar para a configuração do serviço de porta frontal do Azure. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>O nome do host não está Roteando para backend e retorna o código de status 400


### <a name="symptom"></a>Sintoma
- Você criou uma porta frontal, mas uma solicitação para o host de front-end está retornando um código de status HTTP 400.

  - Você criou um mapeamento DNS de um domínio personalizado para o host front-end que você configurou. No entanto, o envio de uma solicitação para o nome de host do domínio personalizado retorna um código de status HTTP 400 e não parece rotear para os back-ends que você configurou.

### <a name="cause"></a>Causa
- Esse sintoma pode acontecer se você não tiver configurado uma regra de roteamento para o domínio personalizado que você adicionou como um host de front-end. Uma regra de roteamento precisa ser explicitamente adicionada para esse host de front-end, mesmo que já tenha sido configurada para o host de front-end no subdomínio da porta frontal (*. azurefd.net) ao qual seu domínio personalizado tem um mapeamento de DNS.

### <a name="troubleshooting-steps"></a>Etapas de solução de problemas
- Adicione uma regra de roteamento do domínio personalizado para o pool de back-end desejado.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>A solicitação para o nome de host front-end retorna 404 código de status

### <a name="symptom"></a>Sintoma
- Você criou uma porta frontal e configurou um host de front-end, um pool de back-ends com pelo menos um back-end e uma regra de roteamento que conecta o host de front-end ao pool de back-ends. Seu conteúdo não parece estar disponível ao enviar uma solicitação para o host de front-end configurado porque um código de status HTTP 404 é retornado.

### <a name="cause"></a>Causa
Há várias causas possíveis para esse sintoma:
 - O back-end não é um back-end voltado para o público e não é visível para o serviço de porta frontal.

- O back-end está configurado incorretamente, o que está fazendo com que o serviço de porta frontal envie a solicitação incorreta (ou seja, seu back-end só aceita HTTP, mas você não selecionou a permissão HTTPS, portanto a porta frontal está tentando encaminhar solicitações HTTPS).
- O back-end está rejeitando o cabeçalho do host que foi encaminhado com a solicitação para o back-end.
- A configuração para o back-end ainda não foi totalmente implantada.

### <a name="troubleshooting-steps"></a>Etapas de solução de problemas
1. Hora da implantação
    - Verifique se você aguardou cerca de 10 minutos para que a configuração seja implantada.

2. Verificar as configurações de back-end
   - Navegue até o pool de back-end ao qual a solicitação deve ser roteada (depende de como você tem a regra de roteamento configurada) e verifique se o _tipo de host back-end_ e o nome do host de back-end estão corretos. Se o back-end for um host personalizado, verifique se você o escreveu corretamente. 

   - Verifique as portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente), estão corretos e nenhuma alteração será necessária. No entanto, há uma chance de que o back-end não esteja configurado dessa maneira e esteja ouvindo em uma porta diferente.

     - Verifique o _cabeçalho de host de back-end_ configurado para os back-ends para os quais o host front-end deve ser roteado. Na maioria dos casos, esse cabeçalho deve ser igual ao _nome do host de back-end_. No entanto, um valor incorreto poderá causar vários códigos de status HTTP 4xx se o back-end esperar algo diferente. Se você inserir o endereço IP do seu back-end, talvez seja necessário definir o _cabeçalho de host de back-end_ para o nome do host do back-end.


3. Verificar as configurações da regra de roteamento
     - Navegue até a regra de roteamento que deve rotear do nome de host front-end em questão para um pool de back-end. Verifique se os protocolos aceitos estão configurados corretamente ou, se não estiverem, certifique-se de que a porta frontal do protocolo usará ao encaminhar a solicitação esteja configurada corretamente. Os _protocolos aceitos_ determinam quais solicitações a porta frontal deve aceitar e o _protocolo de encaminhamento_ determina qual porta frontal do protocolo deve usar para encaminhar a solicitação para o back-end.
          - Por exemplo, se o back-end aceitar somente solicitações HTTP, as seguintes configurações seriam válidas:
               - Os _protocolos aceitos_ são http e HTTPS. O _protocolo de encaminhamento_ é http. A solicitação de correspondência não funcionará, já que HTTPS é um protocolo permitido e, se uma solicitação for enviada como HTTPS, a porta frontal tentará encaminhá-la usando HTTPS.

               - Os _protocolos aceitos_ são http. O _protocolo de encaminhamento_ é uma solicitação de correspondência ou HTTPS.

   - A _regravação de URL_ é desabilitada por padrão e você só deve usar esse campo se quiser restringir o escopo de recursos hospedados por back-end que deseja disponibilizar. Quando desabilitada, a porta da frente encaminhará o mesmo caminho de solicitação que receber. É possível que esse campo esteja configurado incorretamente e a porta da frente esteja solicitando um recurso do back-end que não está disponível, retornando, assim, um código de status HTTP 404.

