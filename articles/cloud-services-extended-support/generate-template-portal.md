---
title: Gerar modelo ARM para serviços na nuvem (suporte alargado) usando o portal Azure
description: Gerar e descarregar modelo ARM e ficheiro de parâmetros para serviços em nuvem (suporte alargado) usando o portal Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077120"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Gerar modelo ARM para serviços na nuvem (suporte alargado) usando o portal Azure

Este artigo explica como descarregar o modelo ARM e o ficheiro de parâmetros do [portal Azure](https://portal.azure.com) para o seu Serviço cloud. O modelo ARM e o ficheiro de parâmetros podem ser usados em implementações via Powershell para criar ou atualizar um serviço de nuvem

## <a name="get-arm-template-via-portal"></a>Obtenha o modelo ARM via portal

  1. Vá ao portal Azure e [crie um novo serviço de nuvem](deploy-portal.md). Adicione a configuração do serviço de nuvem, os ficheiros de pacote e definição. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="A imagem mostra a secção de upload do separador básico durante a criação.":::
  
  2. Uma vez concluídos todos os campos, mude para o separador 'Rever e Criar' para validar a sua configuração de implementação e clique no **modelo de Descarregamento para automatizar** o seu Serviço cloud (suporte alargado).
    :::image type="content" source="media/download-template-portal-1.png" alt-text="A imagem mostra o download do modelo sob o serviço de cloud (suporte alargado) no portal Azure.":::
  
  3. Descarregue o seu modelo e ficheiros de parâmetros. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="A imagem mostra o ficheiro de modelo de descarregamento no portal Azure.":::
  
  4. Copie o Pacote SAS URI e Configuração SAS URI a partir do separador de revisão e crie o separador e adicione-os ao parameter.jsno ficheiro. Estes ficheiros podem agora ser utilizados para criar um novo serviço de cloud via PowerShell.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="A imagem mostra o pacote SAS URI e os parâmetros SAS URI de configuração no portal Azure.":::
  
## <a name="next-steps"></a>Passos seguintes 
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md)
  
