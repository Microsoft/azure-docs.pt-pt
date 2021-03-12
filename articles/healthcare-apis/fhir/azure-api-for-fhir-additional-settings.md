---
title: Definições adicionais para AZure API para FHIR
description: Visão geral das definições adicionais que pode definir para AZure API para FHIR
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 23c4fb6be4e30e78faa1ce411037f828d6518f50
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020255"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Definições adicionais para Azure API para FHIR

Neste guia de como fazer, vamos rever as definições adicionais que você deseja definir no seu API Azure para FHIR. Há páginas adicionais que perfuram ainda mais detalhes.

## <a name="configure-database-settings"></a>Configurar definições de base de dados

A Azure API para FHIR utiliza base de dados para armazenar os seus dados. O desempenho da base de dados subjacente depende do número de Unidades de Pedido (RU) selecionadas durante o fornecimento de serviços ou nas definições de base de dados após o fornecimento do serviço.

A produção deve ser disponibilizada para garantir que estão sempre disponíveis recursos suficientes para a sua base de dados. Quantas RUs precisa para a sua aplicação depende das operações que executa. As operações podem ir desde a leitura simples e escritas a consultas mais complexas.

Para obter mais informações sobre como alterar as definições predefinidas, consulte [as definições da base de dados](configure-database.md)de configuração .

## <a name="access-control"></a>Controlo de acesso

A Azure API para FHIR só permitirá aos utilizadores autorizados o acesso à API FHIR. Pode configurar utilizadores autorizados através de dois mecanismos diferentes. A forma primária e recomendada de configurar o controlo de acesso está a utilizar o [controlo de acesso baseado em funções Azure (Azure RBAC),](../../role-based-access-control/index.yml)que é acessível através da lâmina de controlo de acesso **(IAM).** O Azure RBAC só funciona se quiser garantir o acesso a um plano de dados utilizando o inquilino do Azure Ative Directory associado à sua subscrição. Se desejar utilizar um inquilino diferente, a Azure API para FHIR oferece um mecanismo local de controlo de acesso a plano de dados FHIR. As opções de configuração não são tão ricas quando se utiliza o mecanismo LOCAL RBAC. Para mais detalhes, escolha uma das seguintes opções:

* [Azure RBAC para plano de dados FHIR](configure-azure-rbac.md). Esta é a opção preferida quando está a utilizar o inquilino do Azure Ative Directory associado à sua subscrição.
* [Controlo local de acesso a plano de dados FHIR](configure-local-rbac.md). Utilize esta opção apenas quando precisar de utilizar um inquilino externo do Azure Ative Directory para o controlo de acesso a planos de dados. 

## <a name="enable-diagnostic-logging"></a>Ativar o registo de diagnósticos
Pode querer ativar a sessão de diagnóstico como parte da sua configuração para poder monitorizar o seu serviço e ter relatórios precisos para efeitos de conformidade. Para obter detalhes sobre como configurar a sessão de diagnóstico, consulte o nosso [como-guiar](enable-diagnostic-logging.md) sobre como configurar o registo de diagnóstico, juntamente com algumas consultas de amostra. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>Use cabeçalhos personalizados para adicionar dados a registos de auditoria
Na AZure API para FHIR, pode querer incluir informações adicionais nos registos, que vêm do sistema de chamadas. Para fazer esta informação, você pode usar cabeçalhos personalizados.

Pode utilizar cabeçalhos personalizados para capturar vários tipos de informação. Por exemplo:

* Informações de identidade ou autorização
* Origem do chamador
* Organização originária
* Detalhes do sistema do cliente (registo eletrónico de saúde, portal do paciente)

Para adicionar estes dados aos seus registos de auditoria, consulte os [cabeçalhos HTTP personalizados para adicionar dados aos Registos](use-custom-headers.md) de Auditoria como orientar.

## <a name="next-steps"></a>Passos seguintes

Neste guia de como fazer, configura definições adicionais para a API Azure para FHIR.

Em seguida, confira a série de tutoriais para criar uma aplicação web que leia os dados do FHIR.

>[!div class="nextstepaction"]
>[Implementar a aplicação JavaScript](tutorial-web-app-fhir-server.md)