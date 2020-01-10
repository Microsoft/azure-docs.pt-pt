---
title: Residência de dados da autenticação multifator do Azure
description: Saiba o que os dados pessoais e organizacionais que a autenticação multifator do Azure armazena sobre você e seus usuários e quais dados permanecem dentro do país de origem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4556ece7faaacb8657a1012344f2263ee84214
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480141"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Dados de residência de dados e de cliente para a autenticação multifator do Azure

Os dados do cliente são armazenados pelo Azure AD em uma localização geográfica com base no endereço fornecido pela sua organização ao assinar um serviço online da Microsoft, como o Office 365 e o Azure. Para obter informações sobre onde os dados do cliente são armazenados, você pode usar a seção [onde os dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) da central de confiabilidade da Microsoft.

A autenticação multifator do Azure baseada em nuvem e o processo de Servidor de Autenticação Multifator do Azure e armazenar uma quantidade de dados pessoais e dados organizacionais. Este artigo descreve o que e onde os dados são armazenados.

As seguintes atividades de autenticação multifator se originam em data centers dos EUA, exceto onde indicado:

* A autenticação de dois fatores que usa chamadas telefônicas ou SMS normalmente é originada de data centers dos EUA e é roteada por provedores globais.
    * As solicitações de autenticação de usuário de uso geral de outras regiões, como Europa ou Austrália, atualmente são processadas por data centers nessa região. Outros eventos, como redefinições de senha de autoatendimento, eventos do Azure B2C ou cenários híbridos usando a extensão do NPS ou o adaptador de AD FS, são atualmente processados por data centers dos EUA.
* As notificações por push usando o aplicativo Microsoft Authenticator originam-se de datacenters dos EUA. Além disso, os serviços específicos de fornecedores de dispositivos também podem entrar em cena de regiões diferentes.
* Os códigos OATH geralmente são validados no momento nos EUA.
    * Novamente, os eventos de autenticação de usuário de uso geral que se originam em outras regiões, como Europa ou Austrália, são processados por data centers nessa região. Os eventos adicionais são processados atualmente por data centers dos EUA.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Dados pessoais armazenados pela autenticação multifator do Azure

Dados pessoais são informações de nível de usuário associadas a uma pessoa específica. Os seguintes armazenamentos de dados contêm informações pessoais:

* Usuários bloqueados
* Usuários ignorados
* Microsoft Authenticator solicitações de alteração de token de dispositivo
* Relatórios de atividade da autenticação multifator
* Ativações de Microsoft Authenticator

Essas informações são mantidas por 90 dias.

A autenticação multifator do Azure não registra dados pessoais, como nome de usuário, número de telefone ou endereço IP, mas há um *Userobjectid* que identifica as tentativas de autenticação multifator para os usuários. Os dados de log são armazenados por 30 dias.

### <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

Para nuvens públicas do Azure, excluindo a autenticação do Azure B2C, a extensão do NPS e o Windows Server 2016 ou 2019 AD FS adaptador, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de repositório de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Nos logs da autenticação multifator     |
| SMS unidirecional                          | Nos logs da autenticação multifator     |
| Chamada de voz                           | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada |
| Notificação de Microsoft Authenticator | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada<br />Solicitações de alteração ao Microsoft Authenticator o token de dispositivo é alterado |

> [!NOTE]
> O armazenamento de dados do relatório de atividade de autenticação multifator é armazenado no Estados Unidos para todas as nuvens, independentemente da região que processa a solicitação de autenticação. Microsoft Azure Alemanha, Microsoft Azure operado pela 21Vianet e a nuvem do Microsoft governamental têm seus próprios armazenamentos de dados independentes separados de armazenamentos de dados de região de nuvem pública, no entanto, esses dados são sempre armazenados no Estados Unidos.

Por Microsoft Azure Governamental, Microsoft Azure Alemanha, Microsoft Azure operado por 21Vianet, autenticação do Azure B2C, extensão NPS e adaptador de AD FS do Windows Server 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de repositório de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator |
| SMS unidirecional                          | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator |
| Chamada de voz                           | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada |
| Notificação de Microsoft Authenticator | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada<br />Solicitações de alteração ao Microsoft Authenticator o token de dispositivo é alterado |

### <a name="multi-factor-authentication-server"></a>Servidor Multi-Factor Authentication

Se você implantar e executar o Azure Servidor de Autenticação Multifator, os seguintes dados pessoais serão armazenados:

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais Servidor de Autenticação Multifator para novas implantações. Novos clientes que queiram exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes existentes que ativaram Servidor de Autenticação Multifator antes de 1º de julho poderão baixar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de repositório de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator |
| SMS unidirecional                          | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator |
| Chamada de voz                           | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada |
| Notificação de Microsoft Authenticator | Nos logs da autenticação multifator<br />Armazenamento de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada<br />Solicitações de alteração ao Microsoft Authenticator o token de dispositivo é alterado |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Dados organizacionais armazenados pela autenticação multifator do Azure

Os dados organizacionais são informações de nível de locatário que podem expor configuração ou instalação de ambiente. As configurações de locatário do seguinte portal do Azure páginas de autenticação multifator podem armazenar dados organizacionais, como limites de bloqueio ou informações de ID do chamador para solicitações de autenticação de telefone de entrada:

* Bloqueio de conta
* Alerta de fraudes
* Notificações
* Definições de chamada telefónica

E para o Azure Servidor de Autenticação Multifator, as seguintes portal do Azure páginas podem conter dados organizacionais:

* Definições do servidor
* Omissão de uso individual
* Regras de colocação em cache
* Status de Servidor de Autenticação Multifator

## <a name="log-data-location"></a>Local dos dados de log

O local em que as informações de log são armazenadas depende de em qual região elas são processadas. A maioria das geografias tem recursos nativos de autenticação multifator do Azure, portanto, os dados de log são armazenados na mesma região que processa a solicitação de autenticação multifator. Em geografias sem suporte nativo à autenticação multifator do Azure, eles são atendidos pelo Estados Unidos ou pelas geografias da Europa e os dados de log são armazenados na mesma região que processa a solicitação de autenticação multifator.

Alguns dados de log de autenticação de núcleo só são armazenados no Estados Unidos. Microsoft Azure Alemanha e Microsoft Azure operado pela 21Vianet são sempre armazenados em sua respectiva nuvem. Os dados de log da nuvem do Microsoft governamental sempre são armazenados no Estados Unidos.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre quais informações do usuário são coletadas pela autenticação multifator do Azure baseada em nuvem e pelo Azure Servidor de Autenticação Multifator, consulte [coleta de dados do usuário da autenticação multifator do Azure](howto-mfa-reporting-datacollection.md).
