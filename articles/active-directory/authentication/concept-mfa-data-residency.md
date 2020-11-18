---
title: Residência de dados de autenticação multi-factor Azure AD
description: Saiba quais os dados pessoais e organizacionais Azure AD Multi-Factor Authentication que armazenam sobre si e sobre os seus utilizadores e quais os dados que permanecem dentro do país/região de origem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3854bb410aba8da65b47a335bf13f1dcc51a0180
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839901"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multi-factor-authentication"></a>Residência de dados e dados do cliente para autenticação multi-factor Azure AD

Os dados do cliente são armazenados pela Azure AD numa localização geográfica baseada no endereço fornecido pela sua organização ao subscrever um serviço Microsoft Online como o Microsoft 365 e o Azure. Para obter informações sobre onde os dados do seu cliente são armazenados, pode utilizar a secção [Onde os seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

O processo de autenticação multi-factor Azure AD baseado na nuvem e o processo do Servidor de Autenticação Multi-Factor Azure e armazenam alguma quantidade de dados pessoais e organizacionais. Este artigo descreve o que e onde os dados são armazenados.

O serviço de autenticação multi-factor Azure AD tem datacenters nos EUA, Europa e Ásia-Pacífico. As seguintes atividades são originárias dos centros de dados regionais, exceto se se observar:

* A autenticação multi-factor usando chamadas telefónicas tem origem em datacenters dos EUA e é encaminhada por fornecedores globais.
* Os pedidos gerais de autenticação do utilizador de outras regiões, como a Europa ou a Austrália, são atualmente processados com base na localização do utilizador.
* As notificações push que utilizam a aplicação Microsoft Authenticator são atualmente processadas nos centros de dados regionais com base na localização do utilizador.
    * Os serviços específicos do fornecedor do dispositivo, como as notificações do Apple Push, podem estar fora da localização do utilizador.

## <a name="personal-data-stored-by-azure-ad-multi-factor-authentication"></a>Dados pessoais armazenados por Autenticação Multi-Factor Azure AD

Os dados pessoais são informações de nível de utilizador associadas a uma pessoa específica. As seguintes lojas de dados contêm informações pessoais:

* Utilizadores bloqueados
* Utilizadores ignorados
* Pedidos de alteração de token de dispositivo microsoft Authenticator
* Relatórios de atividade de autenticação multi-factor
* Ativações do Autenticador microsoft

Esta informação é retida por 90 dias.

A autenticação multi-factor Azure AD não regista dados pessoais como nome de utilizador, número de telefone ou endereço IP, mas existe um *UserObjectId* que identifica tentativas de autenticação multi-factor para os utilizadores. Os dados de registo são armazenados durante 30 dias.

### <a name="azure-ad-multi-factor-authentication"></a>Autenticação de vários fatores Azure Ad

Para as nuvens públicas Azure, excluindo a autenticação Azure B2C, extensão NPS e Adaptador AD FS 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Em registos de autenticação multi-factor     |
| SMS unidirecciona                          | Em registos de autenticação multi-factor     |
| Chamada de voz                           | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor<br />Utilizadores bloqueados se fraude reportada |
| Notificação autenticadora da Microsoft | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

> [!NOTE]
> A loja de dados de relatórios de autenticação multi-factor é armazenada nos Estados Unidos para todas as nuvens, independentemente da região que processa o pedido de autenticação. A Microsoft Azure Germany, Microsoft Azure Operada pela 21Vianet e Microsoft Government Cloud têm as suas próprias lojas de dados independentes separadas das lojas de dados da região da nuvem pública, no entanto estes dados são sempre armazenados nos Estados Unidos.

Para o Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure Operado por 21Vianet, autenticação Azure B2C, Extensão NPS e Adaptador AD FS 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor |
| SMS unidirecciona                          | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor |
| Chamada de voz                           | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor<br />Utilizadores bloqueados se fraude reportada |
| Notificação autenticadora da Microsoft | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

### <a name="multi-factor-authentication-server"></a>Servidor Multi-Factor Authentication

Se implementar e executar o Azure Multi-Factor Authentication Server, os seguintes dados pessoais são armazenados:

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o Servidor de Autenticação Multi-Factor para novas implementações. Os novos clientes que gostariam de exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure AD baseada na nuvem. Os clientes existentes que tenham ativado o Servidor de Autenticação Multi-Factor antes do dia 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor |
| SMS unidirecciona                          | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor |
| Chamada de voz                           | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor<br />Utilizadores bloqueados se fraude reportada |
| Notificação autenticadora da Microsoft | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

## <a name="organizational-data-stored-by-azure-ad-multi-factor-authentication"></a>Dados organizacionais armazenados por Autenticação Multi-Factor Azure AD

Os dados organizacionais são informações de nível de inquilino que podem expor a configuração ou configuração do ambiente. As definições do inquilino a partir das seguintes páginas de autenticação multi-factor do portal Azure podem armazenar dados organizacionais, tais como limiares de bloqueio ou informações de identificação do chamador para pedidos de autenticação de telefones recebidos:

* Bloqueio de conta
* Alerta de fraudes
* Notificações
* Definições de chamadas telefónicas

E para o Azure Multi-Factor Authentication Server, as seguintes páginas do portal Azure podem conter dados organizacionais:

* Definições do servidor
* Bypass único
* Regras de caching
* Estado do servidor de autenticação multi-factor

## <a name="log-data-location"></a>Registar localização de dados

Onde a informação de registo é armazenada depende da região em que são processadas. A maioria das geografias tem capacidades de autenticação multi-factor Azure AD nativas, pelo que os dados de registo são armazenados na mesma região que processa o pedido de autenticação multi-factor. Em geografias sem suporte de autenticação multi-factor Azure AD nativo, são servidas pelas geografias dos Estados Unidos ou da Europa e os dados de registo são armazenados na mesma região que processa o pedido de autenticação multi-factor.

Alguns dados de registo de autenticação do núcleo são armazenados apenas nos Estados Unidos. A Microsoft Azure Germany e o Microsoft Azure Operados pela 21Vianet estão sempre armazenados na sua respetiva nuvem. Os dados de registo da Cloud do Governo da Microsoft são sempre armazenados nos Estados Unidos.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre quais as informações do utilizador recolhidas pela autenticação multi-factor Azure AD baseada na nuvem e pelo Servidor de Autenticação Multi-Factor Azure, consulte [a recolha de dados de autenticação multi-factor Azure AD](howto-mfa-reporting-datacollection.md).
