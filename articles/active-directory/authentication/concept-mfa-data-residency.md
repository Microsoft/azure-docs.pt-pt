---
title: Residência de dados de autenticação multi-factor Azure
description: Saiba quais os dados pessoais e organizacionais que a Azure Multi-Factor Authentication armazena sobre si e sobre os seus utilizadores e quais os dados que permanecem dentro do país/região de origem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bed0f6cc32c25563d322da77193c5a3b6072902
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052284"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Residência de dados e dados do cliente para autenticação multi-factor Azure

Os dados do cliente são armazenados pela Azure AD numa localização geográfica baseada no endereço fornecido pela sua organização ao subscrever um serviço Microsoft Online como o Microsoft 365 e o Azure. Para obter informações sobre onde os dados do seu cliente são armazenados, pode utilizar a secção [Onde os seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

O processo de autenticação multi-factor Azure baseado na nuvem e o processo do Servidor de Autenticação Multi-Factor Azure e armazenam uma quantidade de dados pessoais e organizacionais. Este artigo descreve o que e onde os dados são armazenados.

As seguintes atividades de autenticação multi-factor têm atualmente origem nos centros de dados dos EUA, exceto se formos anotados:

* A autenticação de dois fatores utilizando chamadas telefónicas ou SMS normalmente origina-se de datacenters dos EUA e são encaminhados por fornecedores globais.
    * Os pedidos gerais de autenticação dos utilizadores provenientes de outras regiões, como a Europa ou a Austrália, são atualmente tratados por centros de dados nessa região. Outros eventos como resets de palavra-passe de autosserviço, eventos Azure B2C ou cenários híbridos usando extensão NPS ou adaptador AD FS, são todos processados por datacenters dos EUA.
* As notificações push utilizando a aplicação Microsoft Authenticator são originárias de datacenters dos EUA. Além disso, os serviços específicos do fornecedor de dispositivos também podem ser reproduzidores de diferentes regiões.
* Os códigos OATH são normalmente validados nos EUA.
    * Mais uma vez, os eventos de autenticação geral dos utilizadores originários de outras regiões, como a Europa ou a Austrália, são tratados por datacenters naquela região. Eventos adicionais são atualmente processados por datacenters dos EUA.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Dados pessoais armazenados por Autenticação Multi-Factor Azure

Os dados pessoais são informações de nível de utilizador associadas a uma pessoa específica. As seguintes lojas de dados contêm informações pessoais:

* Utilizadores bloqueados
* Utilizadores ignorados
* Pedidos de alteração de token de dispositivo microsoft Authenticator
* Relatórios de atividade de autenticação multi-factor
* Ativações do Autenticador microsoft

Esta informação é retida por 90 dias.

A autenticação multi-factor Azure não regista dados pessoais como nome de utilizador, número de telefone ou endereço IP, mas existe um *UserObjectId* que identifica tentativas de autenticação multi-factor para os utilizadores. Os dados de registo são armazenados durante 30 dias.

### <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

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
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o Servidor de Autenticação Multi-Factor para novas implementações. Os novos clientes que gostariam de exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure baseada na nuvem. Os clientes existentes que tenham ativado o Servidor de Autenticação Multi-Factor antes do dia 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha do OATH                           | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor |
| SMS unidirecciona                          | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor |
| Chamada de voz                           | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor<br />Utilizadores bloqueados se fraude reportada |
| Notificação autenticadora da Microsoft | Em registos de autenticação multi-factor<br />Loja de dados de relatórios de atividade de autenticação multi-factor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o token do dispositivo Microsoft Authenticator muda |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Dados organizacionais armazenados por Autenticação Multi-Factor Azure

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

Onde a informação de registo é armazenada depende da região em que são processadas. A maioria das geografias tem capacidades de autenticação multi-factor nativas, pelo que os dados de registo são armazenados na mesma região que processa o pedido de autenticação multi-factor. Em geografias sem suporte de autenticação multi-factor nativo, são servidas pelas geografias dos Estados Unidos ou da Europa e os dados de registo são armazenados na mesma região que processa o pedido de autenticação multi-factor.

Alguns dados de registo de autenticação do núcleo são armazenados apenas nos Estados Unidos. A Microsoft Azure Germany e o Microsoft Azure Operados pela 21Vianet estão sempre armazenados na sua respetiva nuvem. Os dados de registo da Cloud do Governo da Microsoft são sempre armazenados nos Estados Unidos.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre as informações do utilizador recolhidas pelo Azure Multi-Factor Authentication e pelo Azure Multi-Factor Authentication Server, consulte a recolha de dados de [autenticação multi-factor Azure](howto-mfa-reporting-datacollection.md).
