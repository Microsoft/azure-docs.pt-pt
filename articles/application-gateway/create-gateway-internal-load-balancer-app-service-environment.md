---
title: Troubleshoot um Gateway de Aplicação em Azure – ILB ASE [ Microsoft Docs
description: Aprenda a resolver um portal de aplicações utilizando um Balancer de Carga Interna com um Ambiente de Serviço de Aplicações em Azure
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
ms.openlocfilehash: 4edeea749ba22bef173c15f3a0855679b784ce33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668566"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>O certificado de servidor back-end não está listado para um gateway de aplicação usando um Balancer de Carga Interna com um Ambiente de Serviço de Aplicações

Este artigo aborda o seguinte problema: Um certificado não é listado em branco quando cria um gateway de aplicação utilizando um Balancer de Carga Interna (ILB) juntamente com um App Service Environment (ASE) na parte de trás quando se utiliza TLS de ponta a ponta em Azure.

## <a name="symptoms"></a>Sintomas

Quando cria um portal de aplicação utilizando um ILB com uma ASE na parte de trás, o servidor de back-end pode tornar-se insalubre. Este problema ocorre se o certificado de autenticação do gateway de aplicação não corresponder ao certificado configurado no servidor de back-end. Veja o seguinte cenário como um exemplo:

**Configuração do Gateway da aplicação:**

- **Ouvinte:** Multi-site
- **Porto:** 443
- **Nome do anfitrião:** test.appgwtestase.com
- **Certificado SSL:** CN=test.appgwtestase.com
- **Piscina de backend:** Endereço IP ou FQDN
- **Endereço IP:**: 10.1.5.11
- **Definições HTTP:** HTTPS
- **Porto:**: 443
- **Sonda Personalizada:** Nome de anfitrião - test.appgwtestase.com
- Certificado de **autenticação:** .cer de test.appgwtestase.com
- **Saúde de backend:** Insalubre – O certificado de servidor Backend não está na lista branca com o Application Gateway.

**Configuração ASE:**

- **Ip ILB:** 10.1.5.11
- **Nome de domínio:** appgwtestase.com
- **Serviço de aplicações:** test.appgwtestase.com
- **Ligação SSL:** SNI SSL – CN=test.appgwtestase.com

Ao aceder ao gateway da aplicação, recebe a seguinte mensagem de erro porque o servidor back-end não é saudável:

**502 - O servidor Web recebeu uma resposta inválida enquanto atuava como um gateway ou servidor proxy.**

## <a name="solution"></a>Solução

Quando não utilizar um nome de anfitrião para aceder a um website HTTPS, o servidor back-end devolverá o certificado configurado no website predefinido, caso o SNI seja desativado. Para um ILB ASE, o certificado de predefinição provém do certificado ILB. Se não existirem certificados configurados para o ILB, o certificado provém do certificado da App ASE.

Quando utilizar um nome de domínio totalmente qualificado (FQDN) para aceder ao ILB, o servidor back-end devolverá o certificado correto que é carregado nas definições http. Se não for esse o caso, considere as seguintes opções:

- Utilize o FQDN no conjunto de trás da porta de entrada de aplicações para apontar para o endereço IP do ILB. Esta opção só funciona se tiver uma zona DNS privada ou um DNS personalizado configurado. Caso contrário, tem de criar um registo "A" para um DNS público.

- Utilize o certificado carregado no ILB ou no certificado predefinido (certificado ILB) nas definições http. O gateway de aplicação recebe o certificado quando acede ao IP do ILB para a sonda.

- Utilize um certificado wildcard no ILB e no servidor back-end, de modo que para todos os websites, o certificado é comum. No entanto, esta solução só é possível em caso de subdomínios e não se cada um dos websites necessitar de nomes de anfitriões diferentes.

- Limpe a opção **de serviço de utilização para aplicações** para o gateway da aplicação, caso esteja a utilizar o endereço IP do ILB.

Para reduzir as despesas gerais, pode fazer o upload do certificado ILB nas definições http para que o caminho da sonda funcione. (Este passo é apenas para whitelisting. Não será usado para comunicação TLS.) Pode recuperar o certificado ILB acedendo ao ILB com o seu endereço IP do seu navegador em HTTPS, exportando depois o certificado TLS/SSL num formato CER codificado base-64 e carregando o certificado nas respetivas definições http.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
