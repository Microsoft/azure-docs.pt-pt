---
title: Residência de dados de autenticação multifactor Azure AD
description: Saiba quais os dados pessoais e organizacionais Azure AD que armazenam sobre si e os seus utilizadores e quais os dados que permanecem dentro do país/região de origem.
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
ms.openlocfilehash: 7381ab62eb39c555c6b4eb34f150fc71bea1f10f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561469"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Residência de dados e dados do cliente para a autenticação multifactor Azure AD

O Azure Ative Directory (Azure AD) armazena os dados dos clientes numa localização geográfica baseada no endereço que uma organização fornece ao subscrever um serviço online da Microsoft, como o Microsoft 365 ou o Azure. Para obter informações sobre onde os dados do seu cliente são [armazenados, consulte onde estão os seus dados localizados no](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Microsoft Trust Center.

A autenticação multifactor Azure AD baseada na nuvem e o processo do Servidor de Autenticação Multifactor Azure e armazenam dados pessoais e organizacionais. Este artigo descreve o que e onde os dados são armazenados.

O serviço de autenticação multifactor AZure AD tem datacenters nos Estados Unidos, Europa e Ásia-Pacífico. As seguintes atividades provêm dos centros de dados regionais, exceto se se observar:

* As chamadas telefónicas de autenticação multifactor são originárias de datacenters dos Estados Unidos e são encaminhadas por fornecedores globais.
* Os pedidos de autenticação geral do utilizador de outras regiões são atualmente processados com base na localização do utilizador.
* As notificações push que utilizam a aplicação Microsoft Authenticator são atualmente processadas em datacenters regionais com base na localização do utilizador. Os serviços de dispositivos específicos do fornecedor, como o Apple Push Notification Service, podem estar fora da localização do utilizador.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Dados pessoais armazenados pela autenticação multifactor Azure AD

Os dados pessoais são informações de nível de utilizador que estão associadas a uma pessoa específica. As seguintes lojas de dados contêm informações pessoais:

* Utilizadores bloqueados
* Utilizadores ignorados
* Pedidos de alteração de token de dispositivo microsoft Authenticator
* Relatórios de atividades de autenticação multifactor
* Ativações do Autenticador microsoft

Esta informação é retida por 90 dias.

A autenticação multifactor AD AD não regista dados pessoais como nomes de utilizador, números de telefone ou endereços IP. No entanto, *o UserObjectId* identifica tentativas de autenticação para os utilizadores. Os dados de registo são armazenados durante 30 dias.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Dados armazenados por autenticação multifactor Azure AD

Para as nuvens públicas do Azure, excluindo a autenticação Azure AD B2C, a Extensão NPS e o adaptador de Serviços da Federação de Diretórios Ativos (AD FS) do Windows Server 2016 ou 2019, são armazenados os seguintes dados pessoais:

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Troncos de autenticação multifactor     |
| SMS unidirecciona                          | Troncos de autenticação multifactor     |
| Chamada de voz                           | Troncos de autenticação multifactor<br/>Loja de dados de relatórios de atividade de autenticação multifactor<br/>Utilizadores bloqueados (caso a fraude tenha sido reportada) |
| Notificação autenticadora da Microsoft | Troncos de autenticação multifactor<br/>Loja de dados de relatórios de atividade de autenticação multifactor<br/>Utilizadores bloqueados (caso a fraude tenha sido reportada)<br/>Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

Para o Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure operado por 21Vianet, autenticação AD B2C AD, extensão NPS e adaptador AD FS do Windows Server 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Troncos de autenticação multifactor<br/>Loja de dados de relatórios de atividade de autenticação multifactor |
| SMS unidirecciona                          | Troncos de autenticação multifactor<br/>Loja de dados de relatórios de atividade de autenticação multifactor |
| Chamada de voz                           | Troncos de autenticação multifactor<br/>Loja de dados de relatórios de atividade de autenticação multifactor<br/>Utilizadores bloqueados (caso a fraude tenha sido reportada) |
| Notificação autenticadora da Microsoft | Troncos de autenticação multifactor<br/>Loja de dados de relatórios de atividade de autenticação multifactor<br/>Utilizadores bloqueados (caso a fraude tenha sido reportada)<br/>Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Dados armazenados por Azure Multifactor Authentication Server

Se utilizar o Azure Multifactor Authentication Server, os seguintes dados pessoais são armazenados.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece o Multifactor Authentication Server para novas implementações. Os novos clientes que pretendam exigir a autenticação multifactor dos seus utilizadores devem utilizar a autenticação multifactor Azure AD baseada na nuvem. Os clientes existentes que ativaram o Multifactor Authentication Server antes de 1 de julho de 2019, podem descarregar a versão e atualizações mais recentes e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Troncos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor |
| SMS unidirecciona                          | Troncos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor |
| Chamada de voz                           | Troncos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor<br />Utilizadores bloqueados (caso a fraude tenha sido reportada) |
| Notificação autenticadora da Microsoft | Troncos de autenticação multifactor<br />Loja de dados de relatórios de atividade de autenticação multifactor<br />Utilizadores bloqueados (caso a fraude tenha sido reportada)<br />Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Dados organizacionais armazenados pela autenticação multifactor Azure AD

Os dados organizacionais são informações de nível de inquilino que podem expor configuração ou configuração ambiental. As configurações do inquilino a partir das seguintes páginas de autenticação multifactor do portal Azure podem armazenar dados organizacionais, tais como limiares de bloqueio ou informações de identificação do chamador para pedidos de autenticação de telefones recebidos:

* Bloqueio de conta
* Alerta de fraudes
* Notificações
* Definições de chamadas telefónicas

Para o Azure Multifactor Authentication Server, as seguintes páginas do portal Azure podem conter dados organizacionais:

* Definições do servidor
* Bypass único
* Regras de caching
* Estado do Servidor de Autenticação Multifactor

## <a name="multifactor-authentication-logs-location"></a>Localização dos registos de autenticação multifactor

A tabela seguinte mostra a localização dos registos de serviço para nuvens públicas.

| Cloud pública| Registos de início de sessão | Relatório de atividade de autenticação multifactor        | Registos de serviço de autenticação multifactor       |
|-------------|--------------|----------------------------------------|------------------------|
| Estados Unidos da América          | Estados Unidos da América           | Estados Unidos da América                                     | Estados Unidos da América                     |
| Europa      | Europa       | Estados Unidos da América                                     | Europa <sup>2</sup>    |
| Austrália   | Austrália    | Estados Unidos<sup>1</sup>                         | Austrália <sup>2</sup> |

<sup>1</sup> Os registos de código do OATH são armazenados na Austrália.

<sup>2</sup> Os registos de serviço de autenticação multifactor de chamadas de voz são armazenados nos Estados Unidos.

A tabela seguinte mostra a localização dos registos de serviço para nuvens soberanas.

| Cloud soberana                      | Registos de início de sessão                         | Relatório de atividade de autenticação multifactor (inclui dados pessoais)| Registos de serviço de autenticação multifactor |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Alemanha              | Alemanha                              | Estados Unidos da América                            | Estados Unidos da América               |
| Azure China 21Vianet                 | China                                | Estados Unidos da América                            | Estados Unidos da América               |
| Nuvem do Governo da Microsoft           | Estados Unidos da América                                   | Estados Unidos da América                            | Estados Unidos da América               |

Os relatórios de atividades de autenticação multifactor contêm dados pessoais, como o Nome Principal do Utilizador (UPN) e o número de telefone completo.

Os registos de serviço de autenticação multifactor são utilizados para operar o serviço.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre quais as informações do utilizador recolhidas pela autenticação multifactor Azure AD baseada na nuvem e pelo Azure Multifactor Authentication Server, consulte [a recolha de dados multifactor de autenticação Azure AD](howto-mfa-reporting-datacollection.md).
