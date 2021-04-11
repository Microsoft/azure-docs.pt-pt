---
title: Como instalar o HANA no SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Como instalar o HANA no SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59ea41254c16f32f19a67e7811177a4667a85e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666699"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instale HANA em SAP HANA em Azure (Grandes Instâncias)

Para instalar o HANA no SAP HANA em Azure (Grandes Instâncias), deve primeiro fazer o seguinte:
- Fornece à Microsoft todos os dados para implementar para si numa Grande Instância SAP HANA.
- Você recebe o SAP HANA Large Instance da Microsoft.
- Cria uma rede virtual Azure que está ligada à sua rede no local.
- Liga o circuito ExpressRoute para HANA Large Instances à mesma rede virtual Azure.
- Instala uma máquina virtual Azure que utiliza como caixa de salto para HANA Large Instances.
- Certifique-se de que pode ligar da caixa de salto à sua unidade HANA Large Instance e vice-versa.
- Verifique se todas as embalagens e patches necessários estão instalados.
- Você leu as notas sap e documentação sobre a instalação HANA no sistema operativo que está a usar. Certifique-se de que a libertação de HANA de escolha é suportada na libertação do sistema operativo.

A secção seguinte mostra um exemplo de descarregamento dos pacotes de instalação HANA para a máquina virtual jump box. Neste caso, o sistema operativo é o Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Descarregue os bits de instalação SAP HANA
As unidades HANA Large Instance não estão diretamente ligadas à internet. Não é possível baixar diretamente os pacotes de instalação do SAP para a máquina virtual HANA Large Instance. Em vez disso, descarrega os pacotes para a máquina virtual jump box.

Precisa de um utilizador SAP S ou outro utilizador, que lhe permita aceder ao SAP Marketplace.

1. Inscreva-se e vá ao [SAP Service Marketplace.](https://support.sap.com/en/index.html) Selecione Transferir instalações **de software**  >  **e atualizar** por índice  >  **alfabético**. Em seguida, selecione **Under H – SAP HANA Platform Edition** SAP  >  **HANA Platform Edition 2.0**  >  **Installation**. Descarregue os ficheiros apresentados na imagem seguinte.

   ![Screenshot dos ficheiros para descarregar](./media/hana-installation/image16_download_hana.PNG)

2. Neste exemplo, descarregámos pacotes de instalação SAP HANA 2.0. Na máquina virtual da caixa de salto Azure, expanda os arquivos auto-extrativos para o diretório, como mostrado abaixo.

   ![Screenshot do arquivo auto-extrator](./media/hana-installation/image17_extract_hana.PNG)

3. À medida que os arquivos são extraídos, copie o diretório criado pela extração (neste caso, 51052030). Copie o diretório da unidade HANA Large Instance /hana/volume partilhado num diretório que criou.

   > [!Important]
   > Não copie os pacotes de instalação na raiz ou no arranque LUN, porque o espaço é limitado e precisa de ser usado por outros processos também.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instale o SAP HANA na unidade HANA Large Instance
Para instalar o SAP HANA, inscreva-se como raiz do utilizador. Só a raiz tem permissões suficientes para instalar o SAP HANA. Desa estade permissões no diretório que copiou para /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Se pretender instalar o SAP HANA utilizando a configuração da interface gráfica do utilizador, o pacote GTK2 tem de ser instalado em HANA Large Instances. Para verificar se está instalado, execute o seguinte comando:

```
rpm –qa | grep gtk2
```

(Em etapas posteriores, mostramos a configuração SAP HANA com a interface gráfica do utilizador.)

Entre no diretório de instalações e navegue no sub-directório HDB_LCM_LINUX_X86_64. 

Fora do diretório, comece:

```
./hdblcmgui 
```
Neste ponto, progride através de uma sequência de ecrãs em que fornece os dados para a instalação. Neste exemplo, estamos a instalar o servidor de base de dados SAP HANA e os componentes do cliente SAP HANA. Portanto, a nossa seleção é **SAP HANA Database.**

![Screenshot do ecrã de gestão de ciclo de vida SAP HANA, com base de dados SAP HANA selecionado](./media/hana-installation/image18_hana_selection.PNG)

No ecrã seguinte, **selecione Instalar Novo Sistema**.

![Screenshot do ecrã de gestão de ciclo de vida SAP HANA, com instalação de novo sistema selecionado](./media/hana-installation/image19_select_new.PNG)

Em seguida, selecione entre vários componentes adicionais que pode instalar.

![Screenshot do ecrã de gestão de ciclo de vida SAP HANA, com lista de componentes adicionais](./media/hana-installation/image20_select_components.PNG)

Aqui, escolhemos o Sap HANA Client e o Sap HANA Studio. Também instalamos uma instância de escala. Em seguida, escolha **o sistema de anfitrião único.** 

![Screenshot do ecrã de gestão de ciclo de vida SAP HANA, com sistema de anfitriões único selecionado](./media/hana-installation/image21_single_host.PNG)

Em seguida, forneça alguns dados.

![Screenshot do ecrã de gestão de ciclo de vida SAP HANA, com campos de propriedades do sistema para definir](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Como ID do Sistema HANA (SID), deve fornecer o mesmo SID que forneceu à Microsoft quando encomendou a implementação da HANA Large Instance. A escolha de um SID diferente faz com que a instalação falhe, devido a problemas de permissão de acesso nos diferentes volumes.

Para o caminho de instalação, utilize o diretório /hana/shared. No passo seguinte, fornece as localizações dos ficheiros de dados HANA e dos ficheiros de registo HANA.


![Screenshot do ecrã de gestão de ciclo de vida SAP HANA, com campos de dados e área de log](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> O SID especificado quando definiu as propriedades do sistema (há dois ecrãs) deve coincidir com o SID dos pontos de montagem. Se houver uma incompatibilidade, volte para trás e ajuste o SID ao valor que tem nos pontos de montagem.

No passo seguinte, reveja o nome do anfitrião e, eventualmente, corrija-o. 

![Screenshot do ecrã de gestão de ciclo de vida SAP HANA, com nome de anfitrião](./media/hana-installation/image24_review_host_name.PNG)

No passo seguinte, também precisa de recuperar os dados que deu à Microsoft quando encomendou a implementação da HANA Large Instance. 

![Screenshot da SAP HANA Lifecycle Management, com campos de administrador de sistema para definir](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Forneça o mesmo **ID** do Utilizador do Administrador de Sistema e **ID do Grupo de Utilizador** que forneceu à Microsoft, uma vez que encomenda a implementação da unidade. Caso contrário, a instalação de SAP HANA na unidade HANA Large Instance falha.

Os próximos dois ecrãs não são mostrados aqui. Permitem-lhe fornecer a palavra-passe para o utilizador system da base de dados SAP HANA e a palavra-passe para o utilizador do sapadm. Este último é utilizado para o Agente anfitrião SAP que é instalado como parte da caixa de dados sap HANA.

Depois de definir a palavra-passe, vê um ecrã de confirmação. verifique todos os dados listados e continue com a instalação. Chega-se a um ecrã de progresso que documenta o progresso da instalação, como este:

![Screenshot do ecrã de gestão de ciclo de vida SAP HANA, com indicadores de progresso da instalação](./media/hana-installation/image27_show_progress.PNG)

À medida que a instalação termina, deve ver um ecrã como este:

![Screenshot do ecrã de gestão do ciclo de vida SAP HANA, indicando que a instalação está terminada](./media/hana-installation/image28_install_finished.PNG)

A instância SAP HANA deve estar agora a funcionar e pronta para ser utilizada. Você deve ser capaz de ligar-se a ele a partir do Estúdio SAP HANA. Certifique-se também de que verifica e aplica as últimas atualizações.


## <a name="next-steps"></a>Passos seguintes

- [SAP HANA Grandes Instâncias alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md)

