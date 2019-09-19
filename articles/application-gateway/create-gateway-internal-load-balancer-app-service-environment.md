---
title: Solucionar problemas de um gateway de aplicativo no Azure – ILB ASE | Microsoft Docs
description: Saiba como solucionar problemas de um gateway de aplicativo usando um Load Balancer interno com um Ambiente do Serviço de Aplicativo no Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 9c3216af283ebd9d84a5469d4d50d18c19f67534
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121957"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>O certificado de servidor back-end não está na lista de permissões para um gateway de aplicativo usando um Load Balancer interno com um Ambiente do Serviço de Aplicativo

Este artigo soluciona o problema a seguir: Um certificado não está na lista de permissões quando você cria um gateway de aplicativo usando um Load Balancer interno (ILB) junto com um Ambiente do Serviço de Aplicativo (ASE) no back-end ao usar o SSL de ponta a ponta no Azure.

## <a name="symptoms"></a>Sintomas

Quando você cria um gateway de aplicativo usando um ILB com um ASE no back-end, o servidor back-end pode se tornar não íntegro. Esse problema ocorre se o certificado de autenticação do gateway de aplicativo não corresponder ao certificado configurado no servidor back-end. Consulte o cenário a seguir como exemplo:

**Configuração do gateway de aplicativo:**

- **Listener** Multissite
- **Porto** 443
- **Nome do host:** Test.appgwtestase.com
- **Certificado SSL:** CN = test. appgwtestase. com
- **Pool de back-end:** Endereço IP ou FQDN
- **Endereço IP:** : 10.1.5.11
- **Configurações de HTTP:** HTTPS
- **Porta:** : 443
- **Investigação personalizada:** Nome do host – test.appgwtestase.com
- **Certificado de autenticação:** . cer de Test.appgwtestase.com
- **Integridade do back-end:** Não íntegro – o certificado do servidor back-end não está na lista de permissões com o gateway de aplicativo.

**Configuração do ASE:**

- **ILB IP:** 10.1.5.11
- **Nome de domínio:** appgwtestase.com
- **Serviço de aplicativo:** Test.appgwtestase.com
- **Associação SSL:** SNI SSL – CN = test. appgwtestase. com

Ao acessar o gateway de aplicativo, você recebe a seguinte mensagem de erro porque o servidor back-end não está íntegro:

**502 – o servidor Web recebeu uma resposta inválida ao atuar como um gateway ou servidor proxy.**

## <a name="solution"></a>Solução

Quando você não usar um nome de host para acessar um site HTTPS, o servidor back-end retornará o certificado configurado no site padrão, caso o SNI esteja desabilitado. Para um ASE ILB, o certificado padrão é proveniente do certificado ILB. Se não houver nenhum certificado configurado para o ILB, o certificado será proveniente do certificado do aplicativo ASE.

Quando você usa um FQDN (nome de domínio totalmente qualificado) para acessar o ILB, o servidor back-end retorna o certificado correto que é carregado nas configurações de HTTP. Se esse não for o caso, considere as seguintes opções:

- Use o FQDN no pool de back-end do gateway de aplicativo para apontar para o endereço IP do ILB. Essa opção só funcionará se você tiver uma zona DNS privada ou um DNS personalizado configurado. Caso contrário, você precisa criar um registro "A" para um DNS público.

- Use o certificado carregado no ILB ou o certificado padrão (certificado ILB) nas configurações de HTTP. O gateway de aplicativo obtém o certificado quando acessa o IP do ILB para a investigação.

- Use um certificado curinga no ILB e no servidor back-end, para que, para todos os sites, o certificado seja comum. No entanto, essa solução só é possível em caso de subdomínios e não se cada um dos sites exigir nomes de host diferentes.

- Desmarque a opção **usar para o serviço de aplicativo** para o gateway de aplicativo caso você esteja usando o endereço IP do ILB.

Para reduzir a sobrecarga, você pode carregar o certificado ILB nas configurações de HTTP para fazer o caminho de investigação funcionar. (Essa etapa é apenas para a lista de permissões. Ele não será usado para comunicação SSL.) Você pode recuperar o certificado ILB acessando o ILB com seu endereço IP do seu navegador em HTTPS e exportando o certificado SSL em um formato CER codificado em base-64 e carregando o certificado nas respectivas configurações de HTTP.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
