---
title: Gerir atualizações na descrição geral do Azure Stack | Documentos da Microsoft
description: Saiba mais sobre a gestão de atualizações para os sistemas integrados do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: bf797404b173d6febe133eacbb9d36310160aff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281578"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gerir atualizações na descrição geral do Azure Stack

*Aplica-se a Sistemas integrados do Azure Stack*

Pacotes de atualização da Microsoft para o Azure Stack, normalmente, os sistemas integrados versão em todo a quarta terça-feira de cada mês. Colocar o fabricante de equipamento original (OEM) sobre o seu processo de notificação específicos para garantir que as notificações de atualização atingir a sua organização. Também pode verificar nesta biblioteca de documentação sob **descrição geral** > **notas de versão** para obter informações sobre as versões que estão em suporte Active Directory. 

Cada versão das atualizações de software da Microsoft é fornecido como um pacote de atualização única. Como um operador do Azure Stack, pode importar, instalação e monitorizar o progresso da instalação destas atualizações de pacotes a partir do portal do administrador. 

O fornecedor do OEM também irá lançar atualizações, como atualizações de controladores e firmware. Embora estas atualizações são entregues como pacotes separados pelo fornecedor, estão importados, instalados e geridas da mesma forma como os pacotes de atualização da Microsoft.

Para manter seu sistema em suporte, deve manter atualizado para um nível de versão específica do Azure Stack. Certifique-se de que revê os [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> Não pode aplicar os pacotes de atualização do Azure Stack Development Kit do Azure Stack. Os pacotes de atualização foram concebidos para os sistemas integrados. Para obter informações, consulte [Reimplementar o ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>O fornecedor de recursos de atualização

O Azure Stack inclui um provedor de recursos de atualização que lida com a aplicação das atualizações de software da Microsoft. Este fornecedor verifica que as atualizações são aplicadas em todos os anfitriões físicos, aplicações do Service Fabric e tempos de execução e todas as máquinas de virtuais de infraestrutura e seus serviços associados.

Como instalar atualizações, pode ver o estado de alto nível como destinos de processo de atualização vários subsistemas no Azure Stack (por exemplo, anfitriões físicos e máquinas de virtuais de infraestrutura).

## <a name="plan-for-updates"></a>Plano para atualizações

Recomendamos vivamente que notifique os utilizadores de quaisquer operações de manutenção e que agende as janelas de manutenção normal durante o horário não comercial se possível. Operações de manutenção podem afetar a cargas de trabalho inquilinas e operações do portal.

- Antes de iniciar a instalação desta atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os parâmetros seguintes para validar o status do seu Azure Stack e resolver quaisquer problemas operacionais detectados, incluindo todos os avisos e falhas. Também rever alertas ativos e resolver qualquer um que requerem uma ação.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Mosaico utilização, o atualização para gerir atualizações

Gerir as atualizações a partir do portal do administrador. Como um operador do Azure Stack, pode utilizar o atualizar mosaico no dashboard para:

- ver informações importantes como a versão atual.
- instalar atualizações e monitorizar o progresso.
- rever o histórico de atualização para as atualizações instaladas anteriormente.
- Ver a versão de pacote de OEM da nuvem atual
 
## <a name="determine-the-current-version"></a>Determinar a versão atual

Pode ver a versão atual do Azure Stack no mosaico de atualização. Para abrir o mosaico:

1. Abra o portal de administração do Azure Stack.
2. Selecione **Dashboard**. Na **atualização** mosaico, a versão atual está listado. 

    ![Atualizações de mosaico no dashboard predefinido](./media/azure-stack-updates/image1.png)

    Por exemplo, no ecrã da versão é 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalar atualizações e monitorizar o progresso


1. Abra o portal de administração do Azure Stack.
2. Selecione **Dashboard**. Selecione o mosaico de atualização.
3. Selecione **atualizar agora**.

    ![Detalhes da execução de atualização de pilha do Azure](media/azure-stack-updates/azure-stack-update-button.png)

4.  Pode ver o estado de alto nível como o processo de atualização itera através de vários subsistemas no Azure Stack. Exemplo subsistemas incluem anfitriões físicos, Service Fabric, máquinas de virtuais de infraestrutura e serviços a que fornecem os portais do administrador e do utilizador. Ao longo do processo de atualização, o fornecedor de recursos de atualização comunica detalhes adicionais sobre a atualização, como o número de passos que tiveram êxito, bem como o número em curso.

5. Selecione o **transferir registos completos** da atualização, execute o painel de detalhes para transferir os registos completos.

    ![Detalhes da execução de atualização de pilha do Azure](media/azure-stack-updates/update-run-details.png)

6. Depois de concluído, o fornecedor de recursos de atualização fornece uma **bem-sucedido** confirmação para informá-lo de que o processo de atualização foi concluído e quanto tempo levou. A partir daí, pode ver informações sobre todas as atualizações, as atualizações disponíveis ou as atualizações instaladas com o filtro.

    ![Atualização de pilha do Azure, execute o sucesso de detalhes](media/azure-stack-updates/update-success.png)

   Se a atualização falhar, a atualização de mosaico relatórios **necessita de atenção**. Utilize o **transferir registos completos** para obter um Estado de alto nível de onde a atualização pode ter falhado. A recolha de registos do Azure Stack ajuda a facilitar o diagnóstico e resolução de problemas.

## <a name="next-steps"></a>Passos Seguintes

- [O Azure Stack a política de manutenção](azure-stack-servicing-policy.md) 
- [Gestão da região no Azure Stack](azure-stack-region-management.md)
