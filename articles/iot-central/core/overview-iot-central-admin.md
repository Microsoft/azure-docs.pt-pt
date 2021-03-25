---
title: Guia de administrador central Azure IoT
description: O Azure IoT Central é uma plataforma de aplicações IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral do papel do administrador na IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110496"
---
# <a name="iot-central-administrator-guide"></a>Guia de administrador central IoT

*Este artigo aplica-se aos administradores.*

Uma aplicação IoT Central permite monitorizar e gerir milhões de dispositivos ao longo do seu ciclo de vida. Este guia destina-se a administradores que gerem aplicações IoT Central.

Na IoT Central, um administrador:

- Gere os utilizadores e funções na aplicação.
- Gere a segurança, como a autenticação do dispositivo.
- Configura as definições de aplicação.
- Atualiza aplicações.
- Pedidos de exportação e de ações.
- Monitoriza a saúde da aplicação.

## <a name="users-and-roles"></a>Utilizadores e funções

A IoT Central utiliza um sistema de controlo de acesso baseado em funções para gerir permissões do utilizador dentro de uma aplicação. A IoT Central tem três funções incorporadas para administradores, construtores de soluções e operadores. Um administrador pode criar funções personalizadas com conjuntos específicos de permissões. Um administrador é responsável por adicionar utilizadores a uma aplicação e atribuí-los a funções.

Para saber mais, consulte [Gerir os utilizadores e funções na sua aplicação IoT Central.](howto-manage-users-roles.md)

## <a name="application-security"></a>Segurança de aplicações

Os dispositivos que se ligam à sua aplicação IoT Central normalmente utilizam certificados X.509 ou assinaturas de acesso partilhada (SAS) como credenciais. O administrador gere os certificados ou chaves do grupo de que as credenciais do dispositivo são derivadas.

Para saber mais, consulte a [inscrição em grupo X.509,](concepts-get-connected.md#x509-group-enrollment) [inscrição no grupo SAS](concepts-get-connected.md#sas-group-enrollment)e [como lançar certificados de dispositivo X.509](how-to-roll-x509-certificates.md).

O administrador também pode criar e gerir os tokens API que uma aplicação do cliente utiliza para autenticar com a sua aplicação IoT Central. As aplicações do cliente utilizam a API REST para interagir com a IoT Central.

## <a name="configure-an-application"></a>Configurar uma aplicação

O administrador pode configurar o comportamento e a aparência de uma aplicação IoT Central. Para saber mais, veja:

- [Alterar nome de aplicação e URL](howto-administer.md#change-application-name-and-url)
- [Personalizar a IU](howto-customize-ui.md)
- [Mover uma aplicação para planos de preços diferentes](howto-view-bill.md)
- [Configurar uploads de ficheiros](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Exportar um pedido

Um administrador pode:

- Crie uma cópia de uma aplicação se necessitar apenas de uma cópia duplicada da sua aplicação. Por exemplo, pode precisar de uma cópia duplicada para testes.
- Crie um modelo de aplicação a partir de uma aplicação existente se pretender criar várias cópias.

Para saber mais, consulte [Exporte a sua aplicação Azure IoT](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Migrar para uma nova versão

Um administrador pode migrar uma aplicação para uma versão mais recente. Atualmente, uma aplicação recém-criada é uma aplicação V3. Um administrador pode ter de migrar um pedido V2 para um pedido V3.

Para saber mais, consulte [a migração da sua aplicação V2 IoT Central para V3](howto-migrate.md).

## <a name="monitor-application-health"></a>Monitorizar o estado de funcionamento de aplicações

Um administrador pode usar métricas IoT Central para avaliar a saúde dos dispositivos conectados e a saúde das exportações de dados em execução.

Para visualizar as métricas, um administrador pode usar gráficos no portal Azure, uma API REST ou consultas PowerShell ou Azure CLI.

Para saber mais, consulte [Monitor a saúde geral de uma aplicação IoT Central](howto-monitor-application-health.md).

## <a name="tools"></a>Ferramentas

Muitas das ferramentas que utiliza como administrador estão disponíveis na secção **De Administração** de cada aplicação IoT Central. Também pode utilizar as seguintes ferramentas para completar algumas tarefas administrativas:

- [CLI do Azure](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Portal do Azure](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como administrar a sua aplicação Azure IoT Central, o próximo passo sugerido é aprender sobre [gerir utilizadores e funções](howto-manage-users-roles.md) no Azure IoT Central.
