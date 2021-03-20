---
title: Configurar ferramentas Azure Stream Analytics para Estúdio Visual
description: Este artigo descreve os requisitos de instalação e como configurar as ferramentas Azure Stream Analytics para o Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 0077ac8465e8f785e772b384f26e0edc6874a1a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018705"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instale ferramentas Azure Stream Analytics para Estúdio Visual

Visual Studio 2019 e Visual Studio 2017 suportam Azure Data Lake e Stream Analytics Tools. Este artigo descreve como instalar e desinstalar as ferramentas.

Para obter mais informações sobre a utilização das ferramentas, consulte [Quickstart: Crie um trabalho Azure Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Instalar

As edições visual Studio Enterprise (Ultimate/Premium), Professional e Community apoiam as ferramentas. A edição express e o Visual Studio para Mac não os suportam.

Recomendamos o Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Instalação para Visual Studio 2019 e 2017<a name="recommended-visual-studio-2019-and-2017"></a>

As ferramentas Azure Data Lake e Stream Analytics fazem parte do desenvolvimento do **Azure** e **do armazenamento e processamento** de dados. Ative qualquer uma destas duas cargas de trabalho durante a instalação. Se o Visual Studio já estiver instalado, selecione **Ferramentas**  >  **Obter Ferramentas e Funcionalidades** para adicionar cargas de trabalho.

Baixe [o Visual Studio 2019 (Preview 2 ou superior) ou Visual Studio 2017 (15.3 ou superior)](https://www.visualstudio.com/) e siga as instruções para instalar.

Selecione a carga de trabalho **de armazenamento e processamento de dados** como mostrado:

![É selecionada a carga de trabalho de armazenamento e processamento de dados](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Selecione a carga de trabalho de **desenvolvimento do Azure** como mostrado:

![A carga de trabalho de desenvolvimento do Azure é selecionada](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Depois de adicionar a carga de trabalho, atualize as ferramentas. Este procedimento refere-se ao Visual Studio 2019:

1. **Selecione extensões**  >  **Gerir extensões**.

1. Em **Gerir extensões**, selecione **Atualizações** e escolha **Azure Data Lake e Stream Analytics Tools**.

1. Selecione **Update** para instalar a extensão mais recente.

![Extensões e atualizações do Estúdio Visual](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Instalação para Visual Studio 2015 e 2013<a name="visual-studio-2015-2013"></a>

As edições visual Studio Enterprise (Ultimate/Premium), Professional e Community apoiam as ferramentas. A edição expressa não os apoia.

* Instale o Visual Studio 2015 ou o Visual Studio 2013 Update 4.
* Instale o Microsoft Azure SDK para a versão .NET 2.7.1 ou superior utilizando o [instalador](https://www.microsoft.com/web/downloads/platform.aspx)da plataforma Web .
* Instale [o Microsoft Azure Data Lake e stream Analytics Tools para o Estúdio Visual](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Atualização<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Para o Visual Studio 2019 e o Visual Studio 2017, um novo lembrete de versão aparece como uma notificação do Visual Studio.

Para o Visual Studio 2015 e o Visual Studio 2013, as ferramentas verificam automaticamente as novas versões. Siga as instruções para instalar a versão mais recente.

## <a name="uninstall"></a>Desinstalar

Pode desinstalar as ferramentas Azure Data Lake e Stream Analytics. Para Visual Studio 2019 ou Visual Studio 2017, selecione **Tools**  >  **Get Tools and Features**. Ao **Modificar,** desescolh as ferramentas de análise do **Lago de Dados Azure e do stream.** Aparece sob a carga **de trabalho de armazenamento e processamento de dados** ou a carga de trabalho de desenvolvimento do **Azure.**

Para desinstalar do Visual Studio 2015 ou do Visual Studio 2013, vá a  >  **Programas e Funcionalidades** do Painel de Controlo . Desinstale as ferramentas de análise **do Microsoft Azure Data Lake e stream analytics para o Estúdio Visual**.
