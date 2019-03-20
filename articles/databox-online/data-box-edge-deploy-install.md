---
title: Tutorial sobre como instalar um dispositivo físico do Edge de caixa de dados do Azure | Documentos da Microsoft
description: O segundo tutorial sobre como instalar o Edge de caixa de dados do Azure envolve como descompactar, bastidor e instalar os cabos do dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: ddcaca46a2b8f9501337b3591d6ed666876e1de9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093774"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Tutorial: Instalar o Edge de caixa de dados do Azure (pré-visualização)

Este tutorial descreve como instalar um dispositivo físico do Data Box Edge. O procedimento de instalação envolve desempacotar, montagem de rack e cabos do dispositivo. 

A instalação pode demorar cerca de duas horas a concluir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar em Bastidor o dispositivo
> * Ligar o dispositivo

> [!IMPORTANT]
> A solução de borda de caixa de dados está em pré-visualização. Antes de pedir e implementar esta solução, reveja os [do Azure termos de serviço para pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="prerequisites"></a>Pré-requisitos

Pré-requisitos para instalar um dispositivo físico da seguinte forma:

### <a name="for-the-data-box-edge-resource"></a>Para o recurso do Data Box Edge

Antes de começar, certifique-se de que:

* Concluiu todos os passos [preparar a implementação de borda de caixa de dados do Azure (pré-visualização)](data-box-edge-deploy-prep.md).
    * Acabou de criar um recurso de borda de caixa de dados para implementar o dispositivo.
    * Gerou a chave de ativação para ativar o dispositivo com o recurso do Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Para o dispositivo físico do Data Box Edge

Antes de implementar um dispositivo:

- Certifique-se de que o dispositivo com segurança assenta numa superfície plana, estável e nível de trabalho.
- Verifique se o local onde quer configurar o dispositivo tem:
    - Padrão alternada a partir de uma origem independente

        -OU-
    - Uma unidade de distribuição de energia (PDU) de rack com uma alimentação ininterrupta (UPS)
    - Um slot de 1U disponíveis no rack no qual pretende montar o dispositivo

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Reveja os requisitos de rede para a implementação de borda de caixa de dados e configurar a rede de centro de dados pelos requisitos. Para obter mais informações, veja [Requisitos de rede do Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Certifique-se de que a largura de banda de Internet mínima é 20 Mbps para funcionar ideal do dispositivo.


## <a name="unpack-the-device"></a>Desembalar o dispositivo

Este dispositivo é enviado numa única caixa. Conclua os passos seguintes para desembalar o dispositivo. 

1. Coloque a caixa numa superfície plana e uniforme.
2. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou empacotamento gravemente está danificado, não abri-lo. Contacte o Suporte da Microsoft para ajudar a avaliar se o dispositivo está funcional.
3. Desembale a caixa. Depois de desembalar a caixa, certifique-se de que tem:
    - Um dispositivo Edge de bastidor único
    - Dois cabos de alimentação
    - Kit de montar em Bastidor de uma ferramenta sem slide (dois rails de lado e hardware de montagem são incluídos)

Se não tiver recebido todos os itens listados aqui, contacte o suporte de dados caixa Edge. O passo seguinte consiste em rack montar o seu dispositivo.


## <a name="rack-the-device"></a>Montar o dispositivo em bastidor

O dispositivo tem de ser instalado num bastidor de 19 polegadas padrão. Utilize o procedimento seguinte para rack montar o seu dispositivo no bastidor 19 polegadas padrão com o front-end e postagens rear.

> [!IMPORTANT]
> Os dispositivos Data Box Edge têm de ser montados em bastidor para um funcionamento correto.


1. Solte o botão frontal para desbloquear a calha interior da montagem deslizante. Solte o bloqueio de detenção e pressione a calha central para dentro para a retrair.  
    As calhas interiores e exteriores devem estar agora separadas.

    ![Instalar as calhas de montagem em bastidor](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Instale os rails externas em membros vertical cab rack. Para ajudar com a orientação, os slides do rail são marcados **Front**, e esse fim é afixação em direção à frente do bastidor.    
   1. Localize os pinos na parte frontal e posterior da montagem das calhas. Expanda a calha para encaixar entre os suportes do bastidor. Primeiro, fixe a calha exterior na parte posterior do bastidor. Ajuste o traseiro montar Reto de fecho para posicioná-lo dentro os buracos de montagem de rack rear.   

   2. Pressione e segure o gatilho no suporte posterior para expor os ganchos metálicos. Alinhar e inserir o parêntesis de back-os buracos de montagem e, em seguida, o acionador de versão.

   3. Alinhe o suporte frontal com o orifício de montagem.

   4. O front-Reto de abertura deve ser corrigido agora no rack. Opcionalmente, M5 X screws 10L pode ser utilizado para proteger os rails com postagens, se necessário. 

      ![Instalar as calhas de montagem em bastidor](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Para anexar o rail interna nos chassis, certifique-se de que o aberturas de keyhole do rail interna estão alinhadas com os pins de localização na lateral do chassi. Certifique-se de que as cabeças dos pinos de localização da estrutura estão salientes nas aberturas dos orifícios na calha interior. Puxe a calha para a frente da estrutura até ouvir um clique e a calha encaixar. Repita com a outra calha interior. Pressione a estrutura com a calha interior na parte deslizante para concluir a instalação no bastidor.

    ![Instalar as calhas de montagem em bastidor](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Ligar o dispositivo

Os procedimentos seguintes explicam como ligar o dispositivo Edge em termos de alimentação e rede.

Antes de começar a cablagem o seu dispositivo, precisa do seguinte:

- Desembalar e montar o dispositivo físico do Edge em bastidor.
- Dois cabos de alimentação. 
- Pelo menos um cabo de rede de 1 GbE RJ-45 para ligar à interface de gestão. Existem duas interfaces de rede de 1 GbE, uma de gestão e uma de dados, no dispositivo.
- Um cabo de cobre de 25 GbE SFP+ para cada interface de rede de dados a ser configurada. Interface de rede de dados, pelo menos, um dentre porta 2, porta 3, 4 de porta, porta 5 ou 6 da porta tem de estar ligado à Internet (com uma conectividade para o Azure).  
- Acesso para duas unidades de distribuição de energia (recomendado).

> [!NOTE]
> - Se estiver a ligar apenas uma interface de rede de dados, recomendamos que utilize uma interface de rede de 25 GbE, como as PORTAS 3, 4, 5 ou 6, para enviar dados para o Azure. 
> - Para obter um melhor desempenho e lidar com grandes volumes de dados, considere ligar todas as portas de dados.
> - O dispositivo Edge deve estar ligado à rede do datacenter para que possa ingerir dados dos servidores de origem de dados. 

O dispositivo Edge tem 8 SSDs NVMe. O painel frontal também tem LEDs de estado e botões de energia. O dispositivo inclui unidades de fonte de alimentação de energia redundantes (PSUs), no fundo. O dispositivo tem seis interfaces de rede: duas interfaces de 1 Gbps e quatro interfaces de 25 Gbps. O dispositivo tem um controlador de gestão da placa base (BMC). Identifique as várias portas no painel posterior do dispositivo.
 
  ![Painel posterior de um dispositivo ligado por cabo](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Siga os passos seguintes para instalar os cabos do dispositivo para poder e a rede.

1. Ligue os cabos de alimentação a cada uma das PSUs no bastidor. Para garantir uma elevada disponibilidade, instale e ligue as PSUs a diferentes fontes de alimentação.

2. Ligue os cabos de alimentação às unidades de distribuição de energia (PDUs) em bastidor. Certifique-se de que as duas PSUs utilizam fontes de alimentação separadas.

3. Ligue-se a interface de rede de 1 GbE 1 de porta para o computador que é utilizado para configurar o dispositivo físico. A PORTA 1 é a interface de gestão dedicada.

4. Ligue uma ou mais das PORTAS 2, 3, 4, 5 ou 6 à rede/Internet do datacenter. Se ligar a PORTA 2, utilize o cabo de rede RJ-45. Para as interfaces de rede de 25 GbE, utilize os cabos de cobre SFP+.  


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre tópicos de borda de caixa de dados, como:

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar o dispositivo em bastidor
> * Ligar o dispositivo

Avance para o próximo tutorial para aprender a ligar, configurar e ativar o seu dispositivo.

> [!div class="nextstepaction"]
> [Ligar e configurar dados de caixa de borda](./data-box-edge-deploy-connect-setup-activate.md)


