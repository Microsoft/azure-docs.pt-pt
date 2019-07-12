---
title: Como instalar o HANA no SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Como instalar o HANA no SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce82a2972d9cc349e527811e32c974996327e70b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709750"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instalar o HANA no SAP HANA no Azure (instâncias grandes)

Para instalar o HANA no SAP HANA no Azure (instâncias grandes), tem primeiro de fazer o seguinte:
- Forneça Microsoft todos os dados para implementar para numa instância grande do SAP HANA.
- Receberá a instância grande do SAP HANA da Microsoft.
- Criar uma rede virtual do Azure que está ligada à sua rede no local.
- Ligue o circuito do ExpressRoute para instâncias grandes do HANA para a mesma rede virtual do Azure.
- Instalar uma máquina virtual do Azure que utilizar como uma jumpbox para instâncias grandes do HANA.
- Garantir que pode ligar a partir da jumpbox a sua unidade de instância grande do HANA e vice-versa.
- Verifica se todos os pacotes necessários e os patches estão instalados.
- Leia as notas de SAP e documentação sobre a instalação do HANA no sistema operativo que está a utilizar. Certifique-se de que a versão do HANA à escolha é suportada na versão de sistema operativo.

A secção seguinte mostra um exemplo de download de pacotes de instalação do HANA para a máquina de virtual de caixa de atalhos. Neste caso, o sistema operacional é Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Transferir os bits de instalação do SAP HANA
As unidades de instância grande do HANA não estão diretamente ligadas à internet. Não é possível transferir diretamente os pacotes de instalação do SAP para a máquina virtual de instância grande do HANA. Em vez disso, transfira os pacotes para a máquina de virtual de caixa de atalhos.

Precisa de um utilizador de S SAP ou outro utilizador, o que permite-lhe aceder ao Marketplace de SAP.

1. Inicie sessão e vá para [SAP Service Marketplace](https://support.sap.com/en/index.html). Selecione **baixe softwares** > **instalações e atualização** > **pelo índice alfabética**. Em seguida, selecione **H em – edição da plataforma do SAP HANA** > **2.0 de edição do SAP HANA plataforma** > **instalação**. Baixe os arquivos mostrados na captura de ecrã seguinte.

   ![Captura de ecrã dos ficheiros para transferir](./media/hana-installation/image16_download_hana.PNG)

2. Neste exemplo, iremos transferir pacotes de instalação do SAP HANA 2.0. Sobre o Azure saltar máquina de virtual de caixa, expanda os extração de arquivos no diretório, conforme mostrado abaixo.

   ![Captura de ecrã do arquivo de extração automática](./media/hana-installation/image17_extract_hana.PNG)

3. Como os arquivos são extraídos, copie o diretório criado pela extração (no caso, 51052030). Copie o diretório do volume de /hana/shared de unidade de instância grande do HANA para um diretório que criou.

   > [!Important]
   > Não copie os pacotes de instalação para a raiz ou o arranque LUN, como o espaço é limitado e tem de ser utilizada por outros processos também.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalar o SAP HANA na unidade instância grande do HANA
Para instalar o SAP HANA, inicie sessão como raiz do utilizador. Raiz apenas tem permissões suficientes para instalar o SAP HANA. Definir as permissões no diretório copiadas em /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Se pretender instalar o SAP HANA, utilizando a configuração de interface gráfica do usuário, o pacote de gtk2 tem de ser instalado nas instâncias grandes do HANA. Para verificar se está instalada, execute o seguinte comando:

```
rpm –qa | grep gtk2
```

(Em passos posteriores, vamos mostrar a configuração de SAP HANA com a interface gráfica do usuário.)

Ir para o diretório de instalação e navegue para o diretório de sub-rotina HDB_LCM_LINUX_X86_64. 

Fora nesse diretório, começar:

```
./hdblcmgui 
```
Nesse ponto, avança numa seqüência de telas em que fornecer os dados para a instalação. Neste exemplo, iremos estiver a instalar o servidor de base de dados do SAP HANA e os componentes de cliente do SAP HANA. Por conseguinte, é a nossa seleção **base de dados do SAP HANA**.

![Ecrã de captura de ecrã do SAP HANA Lifecycle Management, com o SAP HANA base de dados selecionada](./media/hana-installation/image18_hana_selection.PNG)

No ecrã seguinte, selecione **instalar o novo sistema**.

![Ecrã de captura de ecrã do SAP HANA Lifecycle Management, sem instalar o sistema novo selecionado](./media/hana-installation/image19_select_new.PNG)

Em seguida, selecione entre os vários componentes adicionais que pode instalar.

![Ecrã de captura de ecrã do SAP HANA Lifecycle Management, com a lista de componentes adicionais](./media/hana-installation/image20_select_components.PNG)

Aqui, escolhemos o cliente do SAP HANA e o SAP HANA Studio. Podemos também instalar uma instância de aumentar verticalmente. Em seguida, escolha **sistema de anfitrião único**. 

![Ecrã de captura de ecrã do SAP HANA Lifecycle Management, com o único sistema anfitrião selecionado](./media/hana-installation/image21_single_host.PNG)

Em seguida, fornece alguns dados.

![Ecrã de captura de ecrã do SAP HANA Lifecycle Management, com campos de propriedades do sistema para definir](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Como ID de sistema HANA (SID), tem de fornecer o mesmo SID à medida que proporcionou à Microsoft quando pediu a implementação de instância grande do HANA. Escolher um SID diferente, faz com que a instalação falhar, devido a problemas de permissão de acesso nos volumes diferentes.

Para o caminho de instalação, utilize o diretório de /hana/shared. No próximo passo, é possível fornecer as localizações para os ficheiros de dados HANA e os ficheiros de registo do HANA.


![Ecrã de captura de ecrã do SAP HANA Lifecycle Management, com campos de área de dados e de registo](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> O SID que especificou quando tiver definido as propriedades do sistema (dois ecrãs atrás) deve corresponder ao SID dos pontos de montagem. Se existir um erro de correspondência, volte atrás e ajustar o SID para o valor que nos pontos de montagem.

No próximo passo, reveja o nome de anfitrião e, eventualmente, corrigi-lo. 

![Ecrã de captura de ecrã do SAP HANA Lifecycle Management, com o nome de anfitrião](./media/hana-installation/image24_review_host_name.PNG)

No próximo passo, terá também de obter dados que deu à Microsoft quando pediu a implementação de instância grande do HANA. 

![Captura de ecrã do SAP HANA ciclo de vida de gestão, com campos de administrador de sistema para definir](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Fornecer o mesmo **ID de utilizador de administrador de sistema** e **ID do grupo de utilizadores** como fornecidos à Microsoft, como solicitar a implementação de unidade. Caso contrário, a instalação do SAP HANA na unidade instância grande do HANA falha.

Os dois ecrãs seguintes não são mostrados aqui. Dão-lhe fornecer a palavra-passe para o usuário do sistema da base de dados SAP HANA e a palavra-passe para o utilizador sapadm. A última é usada para o agente de anfitrião do SAP que é instalado como parte da instância de base de dados do SAP HANA.

Depois de definir a palavra-passe, verá um ecrã de confirmação. Verifique todos os dados apresentados e prosseguir a instalação. Alcance uma tela de progresso que documenta o progresso da instalação, como este:

![Ecrã de captura de ecrã do SAP HANA Lifecycle Management, com os indicadores de progresso de instalação](./media/hana-installation/image27_show_progress.PNG)

Como a instalação estiver concluída, verá um ecrã como esta:

![Ecrã de captura de ecrã do SAP HANA Lifecycle Management, que indica que a instalação estiver concluída](./media/hana-installation/image28_install_finished.PNG)

A instância do SAP HANA deve agora ser até e em execução e está pronto para utilização. Deverá conseguir ligar à mesma a partir do SAP HANA Studio. Além disso, certifique-se de que procurar e aplicar as atualizações mais recentes.


## <a name="next-steps"></a>Passos Seguintes

- [Instâncias grandes do SAP HANA elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md)

