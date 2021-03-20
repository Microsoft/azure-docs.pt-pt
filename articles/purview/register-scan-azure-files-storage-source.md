---
title: Como digitalizar ficheiros Azure
description: Esta forma de orientar descreve detalhes de como digitalizar ficheiros Azure.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554887"
---
# <a name="register-and-scan-azure-files"></a>Registar e digitalizar ficheiros Azure

## <a name="supported-capabilities"></a>Capacidades suportadas

A Azure Files suporta digitalizações completas e incrementais para capturar os metadados e aplicar classificações nos metadados, com base nas classificações do sistema e do cliente.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
- Tem de ser administrador de fonte de dados para configurar e agendar as verificações, consulte [as Permissões do Catálogo](catalog-permissions.md) para obter mais detalhes.

## <a name="register-an-azure-files-storage-account"></a>Registar uma conta de armazenamento Azure Files

Para registar uma nova conta Azure Files no seu catálogo de dados, faça o seguinte:

1. Navegue para o seu Catálogo de Dados de Purga.
1. Selecione **Centro de Gestão** na navegação à esquerda.
1. Selecione **fontes de dados** em **Fontes e digitalização.**
1. Selecione **+ Novo**.
1. Nas **fontes de registo**, selecione **Ficheiros Azure**. Selecione **Continuar**.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="registar nova fonte de dados" border="true":::

No ecrã **'Registar' (Ficheiros Azure),** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo.
1. Escolha como pretende apontar para a sua conta de armazenamento desejada:
   1. **Selecione A partir da subscrição Azure**, selecione a subscrição apropriada da caixa de entrega de assinatura **Azure** e a conta de armazenamento apropriada da caixa de entrega do **nome da conta de Armazenamento.**
   1. Ou, pode selecionar **Entrar manualmente** e introduzir um ponto final de serviço (URL).
1. **Termine** para registar a fonte de dados.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="registar opções de fontes" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>Configurar a autenticação para uma varredura

Para configurar a autenticação para o Armazenamento de Ficheiros Azure utilizando uma chave de conta, faça o seguinte:

1. Selecione o método de autenticação como **Chave conta**.
2. Selecione **A a opção de subscrição Azure.**
3. Escolha a sua subscrição Azure onde existe a conta Azure Files.
4. Escolha o nome da sua conta de armazenamento na lista.
5. Clique em **Concluir**.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)