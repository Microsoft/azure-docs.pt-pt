---
title: Como instalar o HANA no SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Como instalar o HANA no SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d266f458894d93540977c995ff7e8ab71414083f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101284"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instalar o HANA no SAP HANA no Azure (instâncias grandes)

Para instalar o HANA em SAP HANA no Azure (instâncias grandes), você deve primeiro fazer o seguinte:
- Você fornece à Microsoft todos os dados a serem implantados em uma instância grande SAP HANA.
- Você recebe o SAP HANA instância grande da Microsoft.
- Você cria uma rede virtual do Azure que está conectada à sua rede local.
- Você conecta o circuito do ExpressRoute para instâncias grandes do HANA à mesma rede virtual do Azure.
- Você instala uma máquina virtual do Azure que usa como uma caixa de salto para instâncias grandes do HANA.
- Você garante que você pode se conectar da caixa de salto à sua unidade de instância grande do HANA e vice-versa.
- Você verifica se todos os pacotes e patches necessários estão instalados.
- Leia as notas e a documentação do SAP sobre a instalação do HANA no sistema operacional que você está usando. Verifique se a versão do HANA escolhida tem suporte na versão do sistema operacional.

A próxima seção mostra um exemplo de como baixar os pacotes de instalação do HANA para a máquina virtual da caixa de salto. Nesse caso, o sistema operacional é o Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Baixar o SAP HANA bits de instalação
As unidades de instância grande do HANA não estão conectadas diretamente à Internet. Você não pode baixar diretamente os pacotes de instalação do SAP para a máquina virtual de instância grande do HANA. Em vez disso, baixe os pacotes para a máquina virtual da caixa de salto.

Você precisa de um SAP S-User ou outro usuário, que permite acessar o SAP Marketplace.

1. Entre e acesse o [SAP Service Marketplace](https://support.sap.com/en/index.html). Selecione **baixar** > **instalações de software e atualizar** > **por índice alfabético**. Em seguida, selecione **em H – SAP Hana Platform Edition** > SAP Hana**instalação**da**Platform Edition 2,0** > . Baixe os arquivos mostrados na captura de tela a seguir.

   ![Captura de tela dos arquivos a serem baixados](./media/hana-installation/image16_download_hana.PNG)

2. Neste exemplo, baixamos SAP HANA pacotes de instalação 2,0. Na máquina virtual do Azure Jump Box, expanda os arquivos mortos auto-extraíveis para o diretório, conforme mostrado abaixo.

   ![Captura de tela do arquivo auto-extraível](./media/hana-installation/image17_extract_hana.PNG)

3. À medida que os arquivos são extraídos, copie o diretório criado pela extração (nesse caso, 51052030). Copie o diretório do volume/Hana/Shared de unidade de instância grande do HANA para um diretório que você criou.

   > [!Important]
   > Não copie os pacotes de instalação no LUN raiz ou de inicialização, pois o espaço é limitado e também precisa ser usado por outros processos.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalar SAP HANA na unidade de instância grande do HANA
Para instalar SAP HANA, entre como raiz do usuário. Somente a raiz tem permissões suficientes para instalar SAP HANA. Definir permissões no diretório que você copiou em/Hana/Shared.

```
chmod –R 744 <Installation bits folder>
```

Se você quiser instalar SAP HANA usando a configuração de interface gráfica do usuário, o pacote gtk2 precisará ser instalado em instâncias grandes HANA. Para verificar se ele está instalado, execute o seguinte comando:

```
rpm –qa | grep gtk2
```

(Em etapas posteriores, mostramos a configuração do SAP HANA com a interface gráfica do usuário.)

Vá para o diretório de instalação e navegue para o subdiretório HDB_LCM_LINUX_X86_64. 

Fora desse diretório, comece:

```
./hdblcmgui 
```
Neste ponto, você progredi em uma sequência de telas em que fornece os dados para a instalação. Neste exemplo, estamos instalando o servidor de banco de dados SAP HANA e os componentes de cliente do SAP HANA. Portanto, nossa seleção é **SAP Hana banco de dados**.

![Instantâneo da tela de gerenciamento do ciclo de vida SAP HANA, com SAP HANA banco de dados selecionado](./media/hana-installation/image18_hana_selection.PNG)

Na próxima tela, selecione **instalar novo sistema**.

![Captura de tela de gerenciamento do ciclo de vida de SAP HANA, com instalar novo sistema selecionado](./media/hana-installation/image19_select_new.PNG)

Em seguida, selecione entre vários componentes adicionais que você pode instalar.

![Instantâneo da tela de gerenciamento do ciclo de vida SAP HANA, com a lista de componentes adicionais](./media/hana-installation/image20_select_components.PNG)

Aqui, escolhemos o cliente SAP HANA e o SAP HANA Studio. Também instalamos uma instância de expansão. Em seguida, escolha **sistema de host único**. 

![Instantâneo da tela de gerenciamento do ciclo de vida SAP HANA, com o sistema de host único selecionado](./media/hana-installation/image21_single_host.PNG)

Em seguida, forneça alguns dados.

![Instantâneo da tela de gerenciamento do ciclo de vida SAP HANA, com campos de propriedades do sistema para definir](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Como o ID de sistema do HANA (SID), você deve fornecer o mesmo SID que você forneceu à Microsoft quando solicitou a implantação de instância grande do HANA. A escolha de um SID diferente faz com que a instalação falhe, devido a problemas de permissão de acesso nos diferentes volumes.

Para o caminho de instalação, use o diretório/Hana/Shared. Na próxima etapa, você fornece os locais para os arquivos de dados do HANA e os arquivos de log do HANA.


![Instantâneo da tela de gerenciamento do ciclo de vida SAP HANA, com campos de dados e área de log](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> O SID que você especificou quando definiu as propriedades do sistema (duas telas atrás) deve corresponder ao SID dos pontos de montagem. Se houver uma incompatibilidade, volte e ajuste o SID para o valor que você tem nos pontos de montagem.

Na próxima etapa, examine o nome do host e, eventualmente, corrija-o. 

![Instantâneo da tela de gerenciamento do ciclo de vida SAP HANA, com o nome do host](./media/hana-installation/image24_review_host_name.PNG)

Na próxima etapa, você também precisa recuperar os dados que deu à Microsoft quando solicitou a implantação da instância grande do HANA. 

![Captura de tela do gerenciamento do ciclo de vida SAP HANA, com campos de administrador do sistema para definir](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Forneça a mesma **ID de usuário do administrador do sistema** e a **ID do grupo de usuários** que você forneceu à Microsoft, conforme você ordena a implantação da unidade. Caso contrário, a instalação do SAP HANA na unidade de instância grande do HANA falhará.

As duas telas seguintes não são mostradas aqui. Eles permitem que você forneça a senha para o usuário do sistema do banco de dados do SAP HANA e a senha do usuário sapadm. O último é usado para o agente de host do SAP que é instalado como parte da instância do banco de dados SAP HANA.

Depois de definir a senha, você verá uma tela de confirmação. Verifique todos os dados listados e continue com a instalação. Você alcança uma tela de progresso que documenta o progresso da instalação, como esta:

![Instantâneo da tela de gerenciamento do ciclo de vida SAP HANA, com indicadores de progresso da instalação](./media/hana-installation/image27_show_progress.PNG)

À medida que a instalação for concluída, você verá uma tela como esta:

![Instantâneo da tela de gerenciamento do ciclo de vida SAP HANA, indicando que a instalação foi concluída](./media/hana-installation/image28_install_finished.PNG)

A instância de SAP HANA agora deve estar em execução e pronta para uso. Você deve ser capaz de se conectar a ele por meio do SAP HANA Studio. Além disso, certifique-se de verificar e aplicar as atualizações mais recentes.


## <a name="next-steps"></a>Passos Seguintes

- [SAP HANA em Instâncias Grandes alta disponibilidade e recuperação de desastres no Azure](hana-overview-high-availability-disaster-recovery.md)

