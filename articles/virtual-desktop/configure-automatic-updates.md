---
title: Configurar o Gestor de Configuração de Pontos Finais da Microsoft - Azure
description: Como configurar o Microsoft Endpoint Configuration Manager para implementar atualizações de software para o Windows 10 Enterprise multi-sessão no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010128"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Configurar o Gestor de Configuração de Pontos Finais da Microsoft

Este artigo explica como configurar o Gestor de Configuração do Microsoft Endpoint para aplicar automaticamente atualizações a um anfitrião virtual do Windows Desktop que executa o Windows 10 Enterprise multi-sessão.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar esta definição, você precisará das seguintes coisas:

   - Certifique-se de que instalou o Agente gestor de configuração do Microsoft Endpoint nas suas máquinas virtuais.
   - Certifique-se de que a sua versão do Microsoft Endpoint Configuration Manager está pelo menos no nível de ramo 1906. Para obter melhores resultados, utilize o nível de ramo 1910 ou superior.

## <a name="configure-the-software-update-point"></a>Configure the software update point (Configurar o ponto de atualização de software)

Para receber atualizações para várias sessões do Windows 10 Enterprise, é necessário ativar o Windows Server, versão 1903 e mais tarde como um produto dentro do Microsoft Endpoint Configuration Manager. Esta definição de produto também se aplica se utilizar o Serviço de Atualização do Servidor do Windows para implementar atualizações nos seus sistemas.

Para receber atualizações:

1. Abra o Gestor de Configuração do Ponto Final da Microsoft e selecione **Sites.**
2. Selecione **Configurar componentes do site**.
3. Selecione o Ponto de **Atualização** do Software no menu suspenso.
4. Selecione o separador **Products** (Produtos).
5. Selecione a caixa de verificação que diz **Windows Server, versão 1903 e mais tarde**.
6. Vá à **Biblioteca de Software**  >  **Overview** Software  >  **Updates** Todas as  >  **atualizações de software** e selecione **Synchronize Software Updates**.
7. Verifique o ficheiro .log wsyncmgr nos Registos do Gestor de Configuração do **Programa de Ficheiros** do Microsoft  >    >   para se certificar de que as alterações foram guardadas. Pode levar alguns minutos para sincronizar as atualizações.

## <a name="create-a-query-based-collection"></a>Criar uma coleção baseada em consultas

Para criar uma coleção de máquinas virtuais multi-sessão do Windows 10 Enterprise, uma coleção baseada em consultas pode ser usada para identificar o sistema operativo específico SKU.

Para criar uma coleção:

1. Selecione **Ativos e Conformidade**.
2. Vá a Recolhas de Dispositivos **de Visão Geral** e clique com o  >   botão direito As coleções de **dispositivos** e selecione **Create Device Collection** a partir do menu suspenso.
3. No separador **Geral** do menu que abre, insira um nome que descreva a sua coleção no campo **Nome.** No campo **Comentário,** pode dar informações adicionais que descrevam o que é a coleção. Em **Limiting Collection**, defina quais as máquinas que está a incluir na consulta de recolha.
4. No separador Regras de **Adesão,** adicione uma regra para a sua consulta selecionando **a Regra de Adicionar,** selecionando a **Regra de Consulta**.
5. In **Query Rule Properties,** insira um nome para a sua regra e, em seguida, defina os parâmetros da regra selecionando a Declaração de Consulta de **Edição**.
6. Selecione **Declaração de Consulta de Espetáculos**.
7. Na declaração, insira o seguinte cordão:

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. Selecione **OK** para criar a coleção.
9. Para verificar se criou a coleção com sucesso, vá às Coleções de Dispositivos de Visão Geral **de Ativos e**  >    >  Conformidade.
