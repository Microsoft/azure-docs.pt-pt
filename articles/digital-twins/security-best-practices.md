---
title: Entender as práticas recomendadas de segurança-gêmeos digitais do Azure | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas de segurança para o gêmeos digital do Azure e o Internet das Coisas.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 5d2fe5a00d131af54862551991cf984d8576b57e
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860950"
---
# <a name="azure-digital-twins-security-best-practices"></a>Práticas recomendadas de segurança do gêmeos digital do Azure

A segurança de gêmeos digital do Azure permite o acesso preciso a recursos e ações específicas em seu grafo IoT. Ele faz isso por meio de função granular e gerenciamento de permissões chamado [de controle de acesso baseado em função](./security-role-based-access-control.md).

O Azure digital gêmeos também usa outros recursos de segurança que estão presentes no Azure IoT, incluindo Azure Active Directory (Azure AD). Por esse motivo, configurar e proteger aplicativos criados no Azure digital gêmeos envolve o uso de muitas das mesmas [práticas de segurança de IOT do Azure](../iot-fundamentals/iot-security-best-practices.md) atualmente recomendadas.

Este artigo resume as principais práticas recomendadas a serem seguidas.

> [!IMPORTANT]
> Para garantir a segurança máxima do seu espaço IoT, examine recursos de segurança adicionais. Certifique-se de incluir os fornecedores de dispositivo.

> [!TIP]
> Use a [central de segurança do Azure para IOT](https://docs.microsoft.com/azure/asc-for-iot/) para ajudar a detectar ameaças e vulnerabilidades de segurança de IOT.

## <a name="iot-security-best-practices"></a>Melhores práticas de segurança de IoT

Algumas práticas importantes para proteger seus dispositivos IoT com segurança incluem:

> [!div class="checklist"]
> * Proteja cada dispositivo que está conectado ao seu espaço de IoT de uma maneira de prova de adulteração.
> * Limite a função de cada dispositivo, sensor e pessoa no seu espaço IoT. Se for comprometido, o efeito será minimizado.
> * Considere o uso potencial da filtragem de endereço IP do dispositivo e da restrição de porta.
> * Limite a largura de banda de dispositivo e e/s para melhorar o desempenho. A limitação de taxa pode melhorar a segurança, impedindo ataques de negação de serviço.
> * Mantenha o firmware do dispositivo, o sistema operacional e o software atualizados.
> * Auditar e revisar periodicamente práticas recomendadas de segurança de dispositivo, software, rede e gateway conforme elas continuam a melhorar e evoluir.

Algumas práticas importantes para proteger com segurança um espaço IoT incluem:

> [!div class="checklist"]
> * Criptografar dados salvos, armazenados ou persistentes.
> * Exigir que senhas ou chaves sejam alteradas periodicamente ou atualizadas.
> * Restringir cuidadosamente o acesso e as permissões por função. Leia a seção [práticas recomendadas de controle de acesso baseado em função](#role-based-access-control-best-practices) abaixo.
> * Considere uma topologia de rede dividida para que os dispositivos em cada rede sejam isolados dos outros.
> * Use criptografia avançada. Exigir senhas longas, usar protocolos seguros e [autenticação multifator](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Monitorar](./how-to-configure-monitoring.md) Recursos de IoT para observar exceções, ameaças ou parâmetros de recursos que estão fora do intervalo de operação usual. Use a análise do Azure para o gerenciamento de monitoramento.

> [!IMPORTANT]
> Leia [as práticas recomendadas de segurança de IOT](../iot-fundamentals/iot-security-best-practices.md) do Azure para iniciar uma estratégia de segurança de IOT abrangente.

> [!NOTE]
> Para obter mais informações sobre o processamento e o monitoramento de eventos, leia [eventos de rota e mensagens com o gêmeos digital do Azure](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Práticas recomendadas de Azure Active Directory

O Azure digital gêmeos usa [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) para autenticar usuários e proteger aplicativos. O Azure Active Directory dá suporte à autenticação para uma variedade de arquiteturas modernas. Eles são todos baseados em protocolos padrão do setor, como OAuth 2,0 ou OpenID Connect. Algumas práticas importantes para proteger seu espaço IoT para o Azure Active Directory incluem:

> [!div class="checklist"]
> * Armazene chaves e segredos do aplicativo Azure Active Directory em um local seguro, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Use um certificado emitido por uma [autoridade de certificação](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) confiável em vez de segredos do aplicativo para autenticação.
> * Limite o escopo OAuth 2,0 de acesso para um token.
> * Verifique o período de tempo em que um token é válido e se um token permanece válido.
> * Defina os comprimentos de tempo apropriados aos quais os tokens são válidos.
> * Atualizar tokens expirados.

## <a name="role-based-access-control-best-practices"></a>Práticas recomendadas de controle de acesso baseado em função

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as práticas recomendadas do Azure IoT, leia [práticas recomendadas de segurança de IOT](../iot-fundamentals/iot-security-best-practices.md).

* Para saber mais sobre o controle de acesso baseado em função, leia [controle de acesso baseado em função](./security-role-based-access-control.md).

* Para saber mais sobre autenticação, leia [autenticar com APIs](./security-authenticating-apis.md).