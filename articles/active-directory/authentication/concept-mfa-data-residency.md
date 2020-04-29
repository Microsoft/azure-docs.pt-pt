---
title: Residência de dados de autenticação multi-factor Azure
description: Saiba quais os dados pessoais e organizacionais que o Azure Multi-Factor Authentication armazena sobre si e sobre os seus utilizadores e quais os dados que permanecem no país de origem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ce7631c0ce8ab83edc7b9cd31dfe0db3be5d7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309805"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Residência de dados e dados dos clientes para autenticação multi-factor Azure

Os dados dos clientes são armazenados pela Azure AD numa localização geográfica com base no endereço fornecido pela sua organização ao subscrever um serviço Microsoft Online como o Office 365 e o Azure. Para obter informações sobre onde os dados dos seus clientes são armazenados, pode utilizar a secção [Onde estão os seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Autenticação de multifactor estoque azure baseado na nuvem e processo do Servidor de Autenticação Multi-Factor Azure e armazene alguma quantidade de dados pessoais e dados organizacionais. Este artigo descreve o que e onde os dados são armazenados.

As seguintes atividades de autenticação multi-factor são atualmente originárias de datacenters dos EUA, exceto se for em que se nota:

* A autenticação de dois fatores usando chamadas telefónicas ou SMS normalmente são originárias de datacenters dos EUA e são encaminhadas por fornecedores globais.
    * Os pedidos gerais de autenticação dos utilizadores de outras regiões, como a Europa ou a Austrália, são atualmente tratados por datacenters naquela região. Outros eventos, como resets de senha de autosserviço, eventos Azure B2C ou cenários híbridos utilizando extensão NPS ou adaptador AD FS, são atualmente tratados por datacenters dos EUA.
* As notificações push utilizando a aplicação Microsoft Authenticator têm origem em datacenters dos EUA. Além disso, os serviços específicos do fornecedor de dispositivos também podem entrar em jogo de diferentes regiões.
* Os códigos DEJURAMENTO são normalmente validados nos EUA.
    * Mais uma vez, eventos de autenticação de utilizadores com origem noutras regiões, como a Europa ou a Austrália, são processados por datacenters naquela região. Eventos adicionais são atualmente processados por datacenters dos EUA.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Dados pessoais armazenados pela Autenticação Multifactor Azure

Os dados pessoais são informações ao nível do utilizador associadas a uma pessoa específica. As seguintes lojas de dados contêm informações pessoais:

* Utilizadores bloqueados
* Utilizadores ignorados
* Pedidos de alteração de alterações no dispositivo do Microsoft Authenticator
* Relatórios de atividade de autenticação de vários fatores
* Ativações do Autenticador microsoft

Esta informação é retida por 90 dias.

A Autenticação Multi-Factor Azure não regista dados pessoais como nome de utilizador, número de telefone ou endereço IP, mas existe um *UserObjectId* que identifica tentativas de autenticação multi-factor para os utilizadores. Os dados de registo são armazenados durante 30 dias.

### <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

Para nuvens públicas Azure, excluindo a autenticação Azure B2C, extensão NPS e Adaptador AD FS 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha de JURAMENTO                           | Em registos de autenticação de vários fatores     |
| SMS de ida                          | Em registos de autenticação de vários fatores     |
| Chamada de voz                           | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada |
| Notificação do Autenticador da Microsoft | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o dispositivo do Autenticador microsoft mudar |

> [!NOTE]
> A loja de dados de relatório de autenticação multi-factor é armazenada nos Estados Unidos para todas as nuvens, independentemente da região que processa o pedido de autenticação. Microsoft Azure Alemanha, Microsoft Azure Operado s21Vianet, e Microsoft Government Cloud têm as suas próprias lojas de dados independentes separadas das lojas de dados da região pública da nuvem, no entanto estes dados são sempre armazenados nos Estados Unidos.

Para o Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure Operado por 21Vianet, autenticação Azure B2C, Extensão NPS e Adaptador AD FS 2016 ou 2019 AD FS, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha de JURAMENTO                           | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor |
| SMS de ida                          | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor |
| Chamada de voz                           | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada |
| Notificação do Autenticador da Microsoft | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o dispositivo do Autenticador microsoft mudar |

### <a name="multi-factor-authentication-server"></a>Servidor Multi-Factor Authentication

Se implementar e executar o Servidor de Autenticação Multi-Factor Azure, os seguintes dados pessoais são armazenados:

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o Servidor de Autenticação Multi-Factor para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure baseada na nuvem. Os clientes existentes que tenham ativado o Servidor de Autenticação Multi-Factor antes de 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de loja de dados |
|--------------------------------------|-----------------|
| Ficha de JURAMENTO                           | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor |
| SMS de ida                          | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor |
| Chamada de voz                           | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada |
| Notificação do Autenticador da Microsoft | Em registos de autenticação de vários fatores<br />Loja de dados de relatório de atividade de autenticação multifactor<br />Utilizadores bloqueados se fraude reportada<br />Alterar pedidos quando o dispositivo do Autenticador microsoft mudar |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Dados organizacionais armazenados pela Autenticação Multi-Factor Azure

Os dados organizacionais são informações ao nível do inquilino que podem expor a configuração ou a configuração do ambiente. As definições dos inquilinos a partir do seguinte portal Azure Páginas de autenticação multifactor podem armazenar dados organizacionais, tais como limiares de bloqueio ou informações de ID de chamada para pedidos de autenticação do telefone:

* Bloqueio de conta
* Alerta de fraudes
* Notificações
* Definições de chamadas telefónicas

E para o Servidor de Autenticação Multi-Factor Azure, as seguintes páginas do portal Azure podem conter dados organizacionais:

* Definições do servidor
* Bypass único
* Regras de caching
* Estado do servidor de autenticação de vários fatores

## <a name="log-data-location"></a>Localização de dados de registo

Onde a informação de registo é armazenada depende de qual região são processadas. A maioria das geografias tem capacidades de autenticação multi-factor nativas, pelo que os dados de registo são armazenados na mesma região que processa o pedido de autenticação multi-factor. Em geografias sem suporte nativo de autenticação multi-factor Azure, são servidos pelas geografias dos Estados Unidos ou da Europa e os dados de registo são armazenados na mesma região que processa o pedido de autenticação multi-factor.

Alguns dados de registo de autenticação nuclear só são armazenados nos Estados Unidos. Microsoft Azure Alemanha e Microsoft Azure Operados por 21Vianet são sempre armazenados na sua respetiva nuvem. Os dados de registo da Cloud do Governo da Microsoft são sempre armazenados nos Estados Unidos.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as informações do utilizador recolhidas pela autenticação de multifactor azure baseado na nuvem e pelo servidor de autenticação de multi-factors Azure, consulte a recolha de dados dos utilizadores de [autenticação multi-factor Azure.](howto-mfa-reporting-datacollection.md)
