---
title: Compreender as melhores práticas de segurança - Azure Digital Twins / Microsoft Docs
description: Conheça as melhores práticas de segurança para as Gémeas Digitais Azure e a Internet das Coisas.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122564"
---
# <a name="azure-digital-twins-security-best-practices"></a>As melhores práticas de segurança das Gémeas Digitais Azure

A segurança das Gémeas Digitais Azure permite um acesso preciso a recursos e ações específicos no seu gráfico IoT. Fá-lo através do papel granular e da gestão da permissão chamado [controlo de acesso baseado em papéis.](./security-role-based-access-control.md)

A Azure Digital Twins também utiliza outras funcionalidades de segurança que estão presentes no Azure IoT, incluindo o Azure Ative Directory (Azure AD). Por essa razão, configurar e assegurar aplicações construídas em Azure Digital Twins envolve a utilização de muitas das mesmas práticas de [segurança Azure IoT](../iot-fundamentals/iot-security-best-practices.md) atualmente recomendadas.

Este artigo resume as principais práticas a seguir.

> [!IMPORTANT]
> Para garantir a máxima segurança do seu espaço IoT, reveja os recursos de segurança adicionais. Certifique-se de incluir os vendedores de dispositivos.

> [!TIP]
> Utilize o [Azure Security Center para ioT](https://docs.microsoft.com/azure/asc-for-iot/) para ajudar a detetar ameaças e vulnerabilidades de segurança ioT.

## <a name="iot-security-best-practices"></a>Melhores práticas da segurança de IoT

Algumas práticas-chave para proteger com segurança os seus dispositivos IoT incluem:

> [!div class="checklist"]
> * Fixe cada dispositivo que esteja ligado ao seu espaço IoT de forma à prova de adulteração.
> * Limite o papel de cada dispositivo, sensor e pessoa dentro do seu espaço IoT. Se comprometido, o efeito é minimizado.
> * Considere a utilização potencial da filtragem do endereço IP do dispositivo e a restrição da porta.
> * Limite a largura de banda de I/O e do dispositivo para melhorar o desempenho. A limitação das taxas pode melhorar a segurança evitando ataques de negação de serviço.
> * Mantenha o firmware do dispositivo, o sistema operativo e o software atualizados.
> * Audite e reveja periodicamente dispositivos, software, rede e segurança gateway boas práticas à medida que continuam a melhorar e evoluir.
> * Utilize sistemas de segurança, software e dispositivos fidedignos, certificados e compatíveis. Por exemplo, reveja [as ofertas](https://azure.microsoft.com/overview/trusted-cloud/compliance/) de conformidade para a Azure Cloud.

Algumas práticas-chave para garantir um espaço IoT com segurança incluem:

> [!div class="checklist"]
> * Criptografe dados guardados, armazenados ou persistentes.
> * Exija que as palavras-passe ou as chaves sejam periodicamente alteradas ou refrescadas.
> * Restringir cuidadosamente o acesso e as permissões por função. Leia a secção [Controlo de Acesso baseado em papéis nas melhores práticas](#role-based-access-control-best-practices) abaixo.
> * Considere uma topologia de rede dividida para que os dispositivos em cada rede sejam isolados dos outros.
> * Use encriptação poderosa. Exija palavras-passe longas, utilize protocolos seguros e [autenticação de vários fatores.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

[Monitor](./how-to-configure-monitoring.md) Recursos ioT para vigiar os parâmetros de recursos, ameaças ou recursos que se aleijem fora do alcance da operação habitual. Utilize o Azure Analytics para monitorização da gestão.

> [!IMPORTANT]
> Leia as [melhores práticas](../iot-fundamentals/iot-security-best-practices.md) de segurança do Azure IoT para iniciar uma estratégia abrangente de segurança ioT.

> [!NOTE]
> Para obter mais informações sobre processamento e monitorização de eventos, leia [eventos e mensagens da Route com As Gémeas Digitais Azure](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Boas práticas de Diretório Ativo Azure

A Azure Digital Twins utiliza o [Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/authentication/) para autenticar utilizadores e proteger aplicações. O Azure Ative Directory apoia a autenticação para uma variedade de arquiteturas modernas. São todos baseados em protocolos padrão da indústria, como OAuth 2.0 ou OpenID Connect. Algumas práticas-chave para garantir o seu espaço IoT para o Diretório Ativo Azure incluem:

> [!div class="checklist"]
> * Guarde os segredos e chaves da aplicação do Ative Directory da Store Azure num local seguro, como [o Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Utilize um certificado emitido por uma autoridade de [certificados](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) fidedignoem em vez de segredos de aplicação para autenticar.
> * Limite o âmbito de acesso 2.0 da OAuth 2.0 para um símbolo.
> * Verifique se o tempo de um token é válido e se um token permanece válido.
> * Detete os comprimentos de tempo adequados para os dados válidos. Atualização fichas expiradas.
> * Remova **URIs redirecionais** não utilizados e permissões por [controlo de acesso baseado em funções](#role-based-access-control-best-practices).

## <a name="role-based-access-control-best-practices"></a>Práticas de controlo de acesso baseadas em papéis

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as melhores práticas do Azure IoT, leia [as melhores práticas de segurança do IoT.](../iot-fundamentals/iot-security-best-practices.md)

* Para aprender sobre o controlo de acesso baseado em papéis, leia [o controlo de acesso baseado em funções.](./security-role-based-access-control.md)

* Para conhecer a autenticação, leia [Authenticato com APIs](./security-authenticating-apis.md).
