---
title: Resolução de problemas de uma Porta de Aplicação em Azure – ILB ASE | Microsoft Docs
description: Saiba como resolver problemas num gateway de aplicações utilizando um Balanceador de Carga Interna com um Ambiente de Serviço de Aplicações em Azure
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
ms.date: 06/09/2020
ms.author: genli
ms.openlocfilehash: 8861e850e168169762d95c44a54b6a88a036f396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84628523"
---
# <a name="back-end-server-certificate-is-not-allow-listed-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>O certificado de servidor back-end não é permitido listado para um gateway de aplicações usando um Balancer de Carga Interna com um Ambiente de Serviço de Aplicações

Este artigo resoluçe o seguinte problema: Um certificado não é permitido listado quando cria um gateway de aplicação utilizando um Balancer de Carga Interna (ILB) juntamente com um Ambiente de Serviço de Aplicações (ASE) na parte de trás quando se utiliza TLS de ponta a ponta em Azure.

## <a name="symptoms"></a>Sintomas

Quando cria um gateway de aplicação utilizando um ILB com um ASE na parte de trás, o servidor back-end pode tornar-se insalubre. Este problema ocorre se o certificado de autenticação do gateway de aplicações não corresponder ao certificado configurado no servidor de back-end. Veja o seguinte cenário como exemplo:

**Configuração do Gateway de Aplicação:**

- **Ouvinte:** Multi-site
- **Porto:** 443
- **Nome de anfitrião:** test.appgwtestase.com
- **Certificado SSL:** CN=test.appgwtestase.com
- **Piscina de backend:** Endereço IP ou FQDN
- **Endereço IP:** 10.1.5.11
- **DEFINIÇÕES HTTP:** HTTPS
- **Porto:** 443
- **Sonda personalizada:** Nome de anfitrião – test.appgwtestase.com
- **Certificado de Autenticação:** .cer de test.appgwtestase.com
- **Backend Health:** Insalubre – O certificado de servidor backend não é permitido listado com o Gateway de Aplicação.

**Configuração ASE:**

- **IP ILB:** 10.1.5.11
- **Nome de domínio:** appgwtestase.com
- **Serviço de Aplicações:** test.appgwtestase.com
- **Ligação SSL:** SNI SSL – CN=test.appgwtestase.com

Ao aceder ao gateway de aplicações, recebe a seguinte mensagem de erro porque o servidor back-end não é saudável:

**502 - O servidor web recebeu uma resposta inválida enquanto atuava como porta de entrada ou servidor de procuração.**

## <a name="solution"></a>Solução

Quando não utilizar um nome de anfitrião para aceder a um website HTTPS, o servidor back-end devolverá o certificado configurado no website predefinido, caso o SNI esteja desativado. Para um ILB ASE, o certificado de incumprimento provém do certificado ILB. Se não houver certificados configurados para o ILB, o certificado provém do certificado da APP ASE.

Quando utilizar um nome de domínio totalmente qualificado (FQDN) para aceder ao ILB, o servidor back-end devolverá o certificado correto que é carregado nas definições HTTP. Se não for esse o caso, considere as seguintes opções:

- Utilize o FQDN no pool traseiro do gateway de aplicação para indicar o endereço IP do ILB. Esta opção só funciona se tiver uma zona de DNS privada ou um DNS personalizado configurado. Caso contrário, tem de criar um disco "A" para um DNS público.

- Utilize o certificado carregado nas definições ILB ou o certificado de incumprimento (certificado ILB) nas definições HTTP. O gateway de aplicação obtém o certificado quando acede ao IP do ILB para a sonda.

- Utilize um certificado wildcard no ILB e no servidor back-end, de modo que para todos os websites, o certificado é comum. No entanto, esta solução só é possível em caso de subdomínios e não se cada um dos websites necessitar de diferentes nomes de hospedeiros.

- Limpe a opção **de serviço de utilização para aplicação** para o gateway de aplicações no caso de estar a utilizar o endereço IP do ILB.

Para reduzir a sobrecarga, pode carregar o certificado ILB nas definições HTTP para fazer o percurso da sonda funcionar. (Este passo é apenas para permitir a listagem. Não será usado para comunicação TLS.) Pode recuperar o certificado ILB acedendo ao ILB com o seu endereço IP a partir do seu navegador em HTTPS e exportando o certificado TLS/SSL num formato CER codificado base-64 e carregando o certificado nas respetivas definições HTTP.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
