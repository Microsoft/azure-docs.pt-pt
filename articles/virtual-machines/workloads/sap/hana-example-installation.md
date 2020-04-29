---
title: Como instalar a HANA no SAP HANA no Azure (Grandes Instâncias) [ Microsoft Docs
description: Como instalar a HANA no SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 023f32fce01ffbd974b182fa89fd604e62332936
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617218"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instale HANA no SAP HANA em Azure (Grandes Instâncias)

Para instalar hana no SAP HANA no Azure (Grandes Instâncias), deve primeiro fazer o seguinte:
- Fornece à Microsoft todos os dados para ser implantado numa Grande Instância SAP HANA.
- Recebe o SAP HANA Large Instance da Microsoft.
- Cria uma rede virtual Azure que está ligada à sua rede no local.
- Liga o circuito ExpressRoute para as Grandes Instâncias HANA à mesma rede virtual Azure.
- Instala uma máquina virtual Azure que utiliza como caixa de salto para grandes instâncias HANA.
- Certifique-se de que pode ligar-se da caixa de salto à sua unidade HANA Large Instance e vice-versa.
- Verifique se estão instalados todos os pacotes e patches necessários.
- Leu as notas do SAP e documentação sobre a instalação da HANA no sistema operativo que está a utilizar. Certifique-se de que a libertação de escolha da HANA é suportada na libertação do sistema operativo.

A secção seguinte mostra um exemplo de descarregamento dos pacotes de instalação HANA para a máquina virtual jump box. Neste caso, o sistema operativo é o Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Descarregue os bits de instalação SAP HANA
As unidades HANA Large Instance não estão diretamente ligadas à internet. Não é possível descarregar diretamente os pacotes de instalação do SAP para a máquina virtual HANA Large Instance. Em vez disso, descarrega os pacotes para a máquina virtual da caixa de salto.

You need an SAP S-user or other user, which allows you to access the SAP Marketplace.

1. Inscreva-se e vá ao [SAP Service Marketplace.](https://support.sap.com/en/index.html) **Selecione descarregue** > instalações de software**e atualize** > **por índice alfabético**. Em seguida, selecione **Under H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Instalação**. Descarregue os ficheiros apresentados na seguinte imagem.

   ![Screenshot dos ficheiros para descarregar](./media/hana-installation/image16_download_hana.PNG)

2. Neste exemplo, descarregámos pacotes de instalação SAP HANA 2.0. Na máquina virtual da caixa de salto Azure, expanda os arquivos auto-extractos para o diretório, como mostrado abaixo.

   ![Screenshot do arquivo auto-extracto](./media/hana-installation/image17_extract_hana.PNG)

3. À medida que os arquivos são extraídos, copie o diretório criado pela extração (neste caso, 51052030). Copie o diretório da unidade HANA Large Instance /hana/volume partilhado num diretório que criou.

   > [!Important]
   > Não copie os pacotes de instalação na raiz ou arranque LUN, porque o espaço é limitado e precisa de ser usado por outros processos também.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instale o SAP HANA na unidade HANA Large Instance
Para instalar o SAP HANA, inscreva-se como raiz do utilizador. Só a raiz tem permissões suficientes para instalar o SAP HANA. Detete permissões no diretório que copiou para /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Se pretender instalar o SAP HANA utilizando a configuração da interface gráfica do utilizador, a embalagem gtk2 tem de ser instalada em HANA Large Instances. Para verificar se está instalado, execute o seguinte comando:

```
rpm –qa | grep gtk2
```

(Em etapas posteriores, mostramos a configuração SAP HANA com a interface gráfica do utilizador.)

Entre no diretório de instalação e navegue no sub diretório HDB_LCM_LINUX_X86_64. 

Fora desse diretório, comece:

```
./hdblcmgui 
```
Neste ponto, você progride através de uma sequência de ecrãs em que fornece os dados para a instalação. Neste exemplo, estamos a instalar o servidor de base de dados SAP HANA e os componentes do cliente SAP HANA. Portanto, a nossa seleção é **base de dados SAP HANA**.

![Screenshot do ecrã sap HANA Lifecycle Management, com base de dados SAP HANA selecionada](./media/hana-installation/image18_hana_selection.PNG)

No ecrã seguinte, selecione **Instalar novo sistema**.

![Screenshot do ecrã SAP HANA Lifecycle Management, com instalar novo sistema selecionado](./media/hana-installation/image19_select_new.PNG)

Em seguida, selecione entre vários componentes adicionais que pode instalar.

![Screenshot do ecrã SAP HANA Lifecycle Management, com lista de componentes adicionais](./media/hana-installation/image20_select_components.PNG)

Aqui, escolhemos o Cliente SAP HANA e o Estúdio SAP HANA. Também instalamos uma instância de escala. Em seguida, escolha **o sistema de hospedeiro único**. 

![Screenshot do ecrã sap HANA Lifecycle Management, com sistema de anfitriões único selecionado](./media/hana-installation/image21_single_host.PNG)

Em seguida, forneça alguns dados.

![Screenshot do ecrã SAP HANA Lifecycle Management, com campos de propriedades do sistema para definir](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Como HANA System ID (SID), deve fornecer o mesmo SID que forneceu à Microsoft quando encomendou a implementação de Hana Large Instance. A escolha de um SID diferente faz com que a instalação falhe, devido a problemas de permissão de acesso nos diferentes volumes.

Para o percurso de instalação, utilize o diretório /hana/partilhado. No passo seguinte, fornece as localizações dos ficheiros de dados HANA e dos ficheiros de registo HANA.


![Screenshot do ecrã de Gestão de Ciclo de Vida SAP HANA, com dados e campos de área de registo](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> O SID que especificou quando definiu as propriedades do sistema (há dois ecrãs) deve coincidir com o SID dos pontos de montagem. Se houver uma incompatibilidade, volte e ajuste o SID ao valor que tem nos pontos de montagem.

No passo seguinte, reveja o nome do anfitrião e, eventualmente, corrija-o. 

![Screenshot do ecrã SAP HANA Lifecycle Management, com nome anfitrião](./media/hana-installation/image24_review_host_name.PNG)

No passo seguinte, também precisa de recuperar os dados que deu à Microsoft quando ordenou a implementação da HANA Large Instance. 

![Screenshot da SAP HANA Lifecycle Management, com campos de administrador do sistema para definir](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Forneça o mesmo ID e **ID do Utilizador do** Administrador do **Sistema** que forneceu à Microsoft, como encomenda a implementação da unidade. Caso contrário, a instalação do SAP HANA na unidade HANA Large Instance falha.

Os próximos dois ecrãs não são mostrados aqui. Permitem-lhe fornecer a palavra-passe para o utilizador do SISTEMA da base de dados SAP HANA e a palavra-passe para o utilizador do sapadm. Este último é utilizado para o Agente hospedeiro SAP que é instalado como parte da instância de base de dados SAP HANA.

Depois de definir a palavra-passe, consulte um ecrã de confirmação. verifique todos os dados listados e continue com a instalação. Chega-se a um ecrã de progresso que documenta o progresso da instalação, como este:

![Screenshot do ecrã SAP HANA Lifecycle Management, com indicadores de progresso da instalação](./media/hana-installation/image27_show_progress.PNG)

À medida que a instalação termina, deve ver um ecrã como este:

![Screenshot do ecrã SAP HANA Lifecycle Management, indicando que a instalação está terminada](./media/hana-installation/image28_install_finished.PNG)

A instância SAP HANA deve agora estar a funcionar e pronta para ser utilizada. Deves poder ligar-te a ele do Estúdio SAP HANA. Certifique-se também de que verifica e aplica as últimas atualizações.


## <a name="next-steps"></a>Passos seguintes

- [SAP HANA Grandes Instâncias alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md)

