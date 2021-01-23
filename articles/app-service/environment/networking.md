---
title: Rede de Ambiente de Serviço de Aplicações
description: Detalhes de networking de ambiente de serviço de aplicações
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0a1221a8de10fd18768a1a0f0ac08277dc2901d6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735038"
---
# <a name="app-service-environment-networking"></a>Rede de ambiente de serviço de aplicativos

> [!NOTE]
> Este artigo é sobre o App Service Environment v3 (pré-visualização)
> 

O App Service Environment (ASE) é uma única implantação do Serviço de Aplicações Azure que acolhe aplicações web, aplicações api e aplicações de funções. Quando instalar um ASE, escolha a Rede Virtual Azure (VNet) em que pretende que seja implantada. Toda a aplicação de tráfego de entrada e saída estará dentro do VNet que especifica.  

O ASEv3 utiliza duas sub-redes.  Uma sub-rede é utilizada para o ponto final privado que trata do tráfego de entrada. Isto pode ser uma sub-rede pré-existente ou uma nova.  A sub-rede de entrada pode ser utilizada para outros fins que não o ponto final privado. A sub-rede de saída só pode ser utilizada para o tráfego de saída a partir do ASE. Nada mais pode entrar na sub-rede de saída da ASE.

## <a name="addresses"></a>Endereços 
A ASE tem os seguintes endereços na criação:

| Tipo de endereço | descrição |
|--------------|-------------|
| Endereço de entrada | O endereço de entrada é o endereço de ponto final privado utilizado pelo seu ASE. |
| Sub-rede de saída | A sub-rede de saída é também a sub-rede ASE. Durante a pré-visualização, esta sub-rede é utilizada apenas para o tráfego de saída. |
| Endereço de saída do Windows | As aplicações do Windows neste ASE utilizarão este endereço, por padrão, ao fazer chamadas de saída para a internet. |
| Endereço de saída linux | As aplicações Linux neste ASE utilizarão este endereço, por padrão, ao fazer chamadas de saída para a internet. |

O ASEv3 tem detalhes sobre os endereços utilizados pelo ASE na parte ip **addresses** do portal ASE.

![ASE endereça UI](./media/networking/networking-ip-addresses.png)

Se eliminar o ponto final privado utilizado pelo ASE, não poderá contactar as aplicações no seu ASE.  

A ASE utiliza endereços na sub-rede de saída para suportar a infraestrutura utilizada pela ASE. À medida que escala os seus planos de Serviço de Aplicações no seu ASE, utilizará mais endereços. As aplicações na ASE não têm endereços dedicados na sub-rede de saída. Os endereços utilizados por uma aplicação na sub-rede de saída por uma aplicação serão alterados ao longo do tempo.

## <a name="ports"></a>Portas

A ASE recebe tráfego de aplicações nos portos 80 e 443.  Não existem requisitos portuários de entrada ou saída para a ASE. 

## <a name="extra-configurations"></a>Configurações extra

Ao contrário do ASEv2, com o ASEv3 pode definir Grupos de Segurança de Rede (NSGs) e Tabelas de Rota (UDRs) como entender adequado sem restrições. Se quiser forçar o túnel todo o tráfego de saída do seu ASE para um dispositivo NVA. Pode colocar dispositivos WAF na frente de todo o tráfego de entrada para o seu ASE. 

## <a name="dns"></a>DNS

As aplicações no seu ASE utilizarão o DNS com o qual o seu VNet está configurado. Siga as instruções na [Utilização de um Ambiente de Serviço de Aplicações](./using.md#dns-configuration) para configurar o seu servidor DNS para apontar para o seu ASE. Se pretender que algumas aplicações utilizem um servidor DNS diferente do que o seu VNet está configurado, pode defini-lo manualmente numa base de aplicação com as definições da aplicação WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER. A definição da aplicação WEBSITE_DNS_ALT_SERVER configura o servidor DNS secundário. O servidor DNS secundário só é utilizado quando não há resposta do servidor DNS primário. 

## <a name="preview-limitation"></a>Limitação de pré-visualização

Existem algumas funcionalidades de networking que não estão disponíveis com o ASEv3.  As coisas que não estão disponíveis no ASEv3 incluem:

• FTP • Depuração remota • Implantação externa do balançador de carga • Capacidade de aceder a um registo de contentores privados para implantações de contentores • Capacidade de fazer chamadas para Vnets globalmente espreitados • Capacidade de backup/restauro com um ponto final de serviço ou conta de armazenamento seguro de ponto final privado • Capacidade de ter definições de aplicações referências de teclado no ponto final de serviço ou contas de teclado seguras de ponto final privado • Capacidade de utilizar BYOS para um ponto final de serviço ou conta de armazenamento seguro de ponto final privado • Utilização do Observador de Rede ou fluxo NSG no tráfego de saída
    
    