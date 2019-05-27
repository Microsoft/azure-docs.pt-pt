---
title: Compreender as práticas recomendadas de segurança de duplos Digital do Azure | Documentos da Microsoft
description: Os gémeos Digital segurança melhores práticas do Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: v-adgera
ms.openlocfilehash: 1d7194beeac1f6f0034738c842e0fc3a58668a13
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966969"
---
# <a name="security-best-practices"></a>Melhores práticas de segurança

Segurança de duplos Digital do Azure permite que preciso acesso a recursos específicos e ações no seu gráfico de IoT. Isso é feito por meio da função granular e gestão de permissões chamado [controlo de acesso baseado em funções](./security-role-based-access-control.md).

Duplos Digital do Azure também utiliza outros recursos de segurança que estão presentes no Azure IoT, incluindo o Azure Active Directory (Azure AD). Por esse motivo, configurando e protegendo aplicativos baseados em duplos Digital do Azure envolvem a utilização de muitas das mesmas [práticas de segurança do Azure IoT](../iot-fundamentals/iot-security-best-practices.md) recomendado atualmente.

Este artigo resume as principais práticas recomendadas a seguir.

> [!IMPORTANT]
> Para garantir a segurança máximo para o seu espaço de IoT, reveja os recursos de segurança adicional. Certifique-se incluir os seus fornecedores de dispositivos.

## <a name="iot-security-best-practices"></a>Melhores práticas da segurança de IoT

Algumas das principais práticas para proteger os seus dispositivos IoT com segurança incluem:

> [!div class="checklist"]
> * Proteja a cada dispositivo que está ligado ao seu espaço de IoT de uma forma de prova de adulteração.
> * Limite a função de cada pessoa dentro do seu espaço de IoT, sensores e dispositivos. Se comprometido, o efeito é minimizado.
> * Considere a utilização de potencial de IP do dispositivo de filtragem de endereços e restrição de porta.
> * Limitar largura de banda de e/s e dispositivo para melhorar o desempenho. Limitação de velocidade pode aumentar a segurança, impedindo ataques denial-of-service.
> * Mantenha o firmware do dispositivo atualizadas.

Algumas chaves práticas para proteger a um espaço de IoT com segurança incluem:

> [!div class="checklist"]
> * Encripte dados persistentes, guardados ou armazenados.
> * Exigir palavras-passe ou chaves para ser alterada ou atualizada periodicamente.
> * Restringir cuidadosamente o acesso e permissões por função. Consulte a secção [práticas recomendadas de controlo de acesso baseado em funções](#rbac) abaixo.
> * Utilize a encriptação poderosas. Necessitam de senhas longas e utilize protocolos seguros e a autenticação de dois fatores.

[Monitor](./how-to-configure-monitoring.md) recursos de IoT para deteção de valores atípicos, ameaças ou parâmetros do recurso que fique fora do intervalo da operação normal. Utilize a análise do Azure para gestão de monitorização.

> [!NOTE]
> Para obter mais informações sobre o processamento de eventos e monitorização, consulte [encaminhar eventos e mensagens duplos Digital do Azure](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Práticas recomendadas do Active Directory do Azure

Duplos Digital do Azure utiliza o Azure Active Directory para autenticar os utilizadores e proteger as aplicações. O Azure Active Directory suporta a autenticação para uma variedade de modernas arquiteturas. Todos elas são baseadas em protocolos de norma da indústria, como o OAuth 2.0 ou OpenID Connect. Alguns principais práticas para proteger o seu espaço de IoT do Azure Active Directory incluem:

> [!div class="checklist"]
> * Store chaves e segredos de aplicações do Azure Active Directory numa localização segura, tal como [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Utilizar um certificado emitido por uma confiança [autoridade de certificação](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) em vez de segredos de aplicação para autenticar.
> * Limitar o âmbito de OAuth 2.0 de acesso de um token.
> * Verifique se o período de tempo, que um token é válido e se um token permanece válido.
> * Definir apropriados períodos de tempo que tokens são válidos para.
> * Atualize tokens expirados.

<div id="rbac"></div>

## <a name="role-based-access-control-best-practices"></a>Práticas recomendadas de controlo de acesso baseado em funções

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre as melhores práticas de IoT do Azure, leia [práticas recomendadas de segurança de IoT](../iot-fundamentals/iot-security-best-practices.md).

* Para saber mais sobre o controlo de acesso baseado em funções, leia [controlo de acesso baseado em funções](./security-role-based-access-control.md).

* Para saber mais sobre autenticação, leia [autenticar com as APIs](./security-authenticating-apis.md).
