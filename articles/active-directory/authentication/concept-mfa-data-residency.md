---
title: Residência de dados de autenticação multifactor Azure AD
description: Saiba quais os dados pessoais e organizacionais Azure AD Multifactor Authentication armazena sobre si e os seus utilizadores e quais os dados que permanecem dentro do país/região de origem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208357"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Residência de dados e dados do cliente para autenticação multifactor Azure AD

Os dados do cliente são armazenados pela Azure AD numa localização geográfica baseada no endereço fornecido pela sua organização ao subscrever um serviço Microsoft Online como o Microsoft 365 e o Azure. Para obter informações sobre onde os dados do seu cliente são armazenados, pode utilizar a secção [Onde os seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

O processo de autenticação multifactor Azure AD baseado na nuvem e o processo do Servidor de Autenticação Multifactor AZure AD e armazenam alguma quantidade de dados pessoais e organizacionais. Este artigo descreve o que e onde os dados são armazenados.

O serviço de autenticação multifactor Azure AD tem datacenters nos EUA, Europa e Ásia-Pacífico. As seguintes atividades são originárias dos centros de dados regionais, exceto se se observar:

* A autenticação multifactor utilizando chamadas telefónicas tem origem em datacenters dos EUA e é encaminhada por fornecedores globais.
* Os pedidos gerais de autenticação do utilizador de outras regiões, como a Europa ou a Austrália, são atualmente processados com base na localização do utilizador.
* As notificações push que utilizam a aplicação Microsoft Authenticator são atualmente processadas nos centros de dados regionais com base na localização do utilizador.
    * Os serviços específicos do fornecedor do dispositivo, como as notificações do Apple Push, podem estar fora da localização do utilizador.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Dados pessoais armazenados por Autenticação Multifactor Azure AD

Os dados pessoais são informações de nível de utilizador associadas a uma pessoa específica. As seguintes lojas de dados contêm informações pessoais:

* Utilizadores bloqueados
* Utilizadores ignorados
* Pedidos de alteração de token de dispositivo microsoft Authenticator
* Relatórios de atividades de autenticação multifactor
* Ativações do Autenticador microsoft

Esta informação é retida por 90 dias.

A Azure AD Multifactor Authentication não regista dados pessoais como nome de utilizador, número de telefone ou endereço IP, mas existe um *UserObjectId* que identifica tentativas de Autenticação Multifactor para os utilizadores. Os dados de registo são armazenados durante 30 dias.

### <a name="azure-ad-multifactor-authentication"></a>Autenticação multifactor Azure Ad

Para as nuvens públicas Azure, excluindo a autenticação Azure B2C, extensão NPS e Adaptador AD FS 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Em registos de autenticação multifactor     |
| SMS unidirecciona                          | Em registos de autenticação multifactor     |
| Chamada de voz                           | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada |
| Notificação autenticadora da Microsoft | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

Para o Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure Operado por 21Vianet, autenticação Azure B2C, Extensão NPS e Adaptador AD FS 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor |
| SMS unidirecciona                          | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor |
| Chamada de voz                           | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada |
| Notificação autenticadora da Microsoft | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

### <a name="multifactor-authentication-server"></a>Servidor de autenticação multifactor

Se implementar e executar o Azure AD Multifactor Authentication Server, os seguintes dados pessoais são armazenados:

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o Multifactor Authentication Server para novas implementações. Os novos clientes que gostariam de exigir a autenticação multifactor dos seus utilizadores devem utilizar a Autenticação Multifactor AZure AD baseada na nuvem. Os clientes existentes que tenham ativado o Multifactor Authentication Server antes do dia 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor |
| SMS unidirecciona                          | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor |
| Chamada de voz                           | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada |
| Notificação autenticadora da Microsoft | Em registos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Dados organizacionais armazenados pela Autenticação Multifactor Azure AD

Os dados organizacionais são informações de nível de inquilino que podem expor a configuração ou configuração do ambiente. As definições do inquilino a partir das seguintes páginas de autenticação multifactor do portal Azure podem armazenar dados organizacionais, tais como limiares de bloqueio ou informações de identificação do chamador para pedidos de autenticação de telefones recebidos:

* Bloqueio de conta
* Alerta de fraudes
* Notificações
* Definições de chamadas telefónicas

E para o Azure AD Multifactor Authentication Server, as seguintes páginas do portal Azure podem conter dados organizacionais:

* Definições do servidor
* Bypass único
* Regras de caching
* Estado do Servidor de Autenticação Multifactor

## <a name="multifactor-authentication-logs-location"></a>Localização dos registos de autenticação multifactor

A tabela seguinte mostra a localização dos registos de serviço para nuvens públicas.

| Cloud pública| Registos de início de sessão | Relatório de atividades de autenticação multifactor        | Registos de serviço de autenticação multifactor       |
|-------------|--------------|----------------------------------------|------------------------|
| EUA          | EUA           | EUA                                     | EUA                     |
| Europa      | Europa       | EUA                                     | Europa <sup>2</sup>    |
| Austrália   | Austrália    | EUA<sup>1</sup>                         | Austrália <sup>2</sup> |

<sup>1</sup> Os registos de código do OATH são armazenados na Austrália

<sup>2</sup> Os registos de serviço de autenticação multifactor de chamadas de voz são armazenados nos EUA

A tabela seguinte mostra a localização dos registos de serviço para nuvens soberanas.

| Cloud soberana                      | Registos de início de sessão                         | Relatório de atividade de autenticação multifactor (inclui dados pessoais)| Registos de serviço de autenticação multifactor |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Alemanha              | Alemanha                              | EUA                            | EUA               |
| Microsoft Azure Operado por 21Vianet | China                                | EUA                            | EUA               |
| Nuvem do Governo da Microsoft           | EUA                                   | EUA                            | EUA               |

Os dados do relatório de atividade de autenticação multifactor contêm dados pessoais, como o nome principal do utilizador (UPN) e o número de telefone completo.

Os registos de serviço de autenticação multifactor são utilizados para operar o serviço.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre quais as informações do utilizador recolhidas pela autenticação multifactor Azure AD e pelo Servidor de Autenticação Multifactor AZure AD, consulte a recolha de [dados de autenticação multifactor AZure AD](howto-mfa-reporting-datacollection.md).
