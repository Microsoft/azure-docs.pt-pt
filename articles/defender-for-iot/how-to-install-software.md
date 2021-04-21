---
title: Defender para instalação IoT
description: Saiba como instalar um sensor e a consola de gestão no local para O Azure Defender para IoT.
ms.date: 4/20/2021
ms.topic: how-to
ms.openlocfilehash: e8366a3408e64d95e6c4d50e3ddef84309b4e8e5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829738"
---
# <a name="defender-for-iot-installation"></a>Defender para instalação IoT

Este artigo descreve como instalar os seguintes elementos do Azure Defender para IoT:

- **Sensor**: O Defender para sensores IoT recolhe o tráfego da rede ICS utilizando uma monitorização passiva (sem agente). Passivo e não intrusivo, os sensores têm zero impacto nas redes e dispositivos OT e IoT. O sensor liga-se a uma porta SPAN ou à rede TAP e começa imediatamente a monitorizar a sua rede. As deteções aparecem na consola do sensor. Lá, você pode ver, investigar e analisá-los em um mapa de rede, inventário de dispositivos, e uma ampla gama de relatórios. Exemplos incluem relatórios de avaliação de risco, consultas de mineração de dados e vetores de ataque. Leia mais sobre as capacidades dos sensores no Defender para o [Guia do Utilizador do Sensor IoT (download direto)](./getting-started.md).

- **Consola de gestão no local**: A consola de gestão no local permite-lhe realizar gestão de dispositivos, gestão de riscos e gestão de vulnerabilidades. Também pode usá-lo para realizar monitorização de ameaças e resposta a incidentes em toda a sua empresa. Fornece uma visão unificada de todos os dispositivos de rede, principais indicadores de risco de IoT e alertas de risco de OT detetados em instalações onde os sensores são implantados. Utilize a consola de gestão no local para visualizar e gerir sensores em redes com lacunas de ar.

Este artigo abrange as seguintes informações de instalação:

  - **Hardware:** Detalhes do aparelho físico Dell e HPE.

  - **Software:** Instalação de software de consola de gestão de sensores e no local.

  - **Aparelhos Virtuais:** Detalhes da máquina virtual e instalação de software.

Após a instalação, ligue o seu sensor à sua rede.

## <a name="about-defender-for-iot-appliances"></a>Sobre o Defender para aparelhos IoT 

As seguintes secções fornecem informações sobre o Defender para aparelhos de sensor IoT e o aparelho para a consola de gestão de IoT no local.

### <a name="physical-appliances"></a>Aparelhos físicos

O sensor de aparelhos Defender para IoT liga-se a uma porta SPAN ou à rede TAP e começa imediatamente a recolher o tráfego da rede ICS utilizando uma monitorização passiva (sem agente). Este processo tem zero impacto nas redes e dispositivos OT porque não é colocado na via dos dados e não digitaliza ativamente dispositivos OT.

Estão disponíveis os seguintes aparelhos de montagem de cremalheira:

| **Tipo de implantação** | **Empresarial** | **Enterprise** | **SMB** | **Linha** |
|--|--|--|--|--|
| **Modelação** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Portas de monitorização** | até 15 RJ45 ou 8 OPT | até 9 RJ45 ou 6 OPT | até 8 RJ45 ou 6 OPT | 4 RJ45 |
| **Max Bandwidth\*** | 3 Gb/Seg | 1 Gb/Seg | 1 Gb/Seg | 100 Mb/Seg |
| **Dispositivos Max Protegidos** | 30,000 | 10,000 | 15 000 | 1,000 |

*A capacidade máxima de largura de banda pode variar dependendo da distribuição do protocolo.

### <a name="virtual-appliances"></a>Aplicações virtuais

Estão disponíveis os seguintes aparelhos virtuais:

| **Tipo de implantação** | **Enterprise** | **SMB** | **Linha** |
|--|--|--|--|
| **Descrição** | Aparelho virtual para implementações empresariais | Aparelho virtual para implantações SMB | Aparelho virtual para implementações de linha |
| **Max Bandwidth\*** | 150 Mb/seg | 15 Mb/seg | 3 Mb/seg |
| **Dispositivos max protegidos** | 3.000 | 300 | 100 |
| **Tipo de implantação** | Grandes Empresas | SMB | Linha |
| **Descrição** | Aparelho virtual para implementações empresariais | Aparelho virtual para implantações SMB | Aparelho virtual para implementações de linha |

*A capacidade máxima de largura de banda pode variar dependendo da distribuição do protocolo.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Especificações de hardware para a consola de gestão no local

 | Item | Descrição |
 |----|--|
 **Descrição** | Numa arquitetura multifunções, a consola de gestão no local proporciona visibilidade e controlo em sites geograficamente distribuídos. Integra-se com pilhas de segurança SOC, incluindo SIEMs, sistemas de bilhética, firewalls de próxima geração, plataformas de acesso remoto seguras e o Defender para a caixa de areia de malware IoT ICS. |
 **Tipo de implantação** | Grandes Empresas |
 **Tipo de aparelho**  | Dell R340, VM |
 **Número de sensores geridos** | Ilimitado |

## <a name="prepare-for-the-installation"></a>Preparar para a instalação

### <a name="access-the-iso-installation-image"></a>Aceda à imagem de instalação iso

A imagem de instalação está acessível a partir do portal Defender para IoT.

Para aceder ao ficheiro:

1. Inscreva-se na sua conta Defender para IoT.

1. Aceda ao **sensor 'Rede'** ou à página **da consola de gestão de instalações** e selecione uma versão para descarregar.

### <a name="install-from-dvd"></a>Instalação a partir de DVD

Antes da instalação, certifique-se de que tem:

- Uma unidade de DVD portátil com o conector USB.

- Uma imagem do instalador ISO.

Para instalar:

1. Queime a imagem num DVD ou prepare um disco numa chave. Ligue uma unidade de DVD portátil ao computador, clique com o botão direito na imagem ISO e selecione **Burn to disk**.

1. Ligue o DVD ou o disco numa chave e configuure o aparelho para arrancar a partir de DVD ou disco numa chave.

### <a name="install-from-disk-on-a-key"></a>Instalar a partir de disco numa chave

Antes da instalação, certifique-se de que tem:

  - Rufus instalado.
  
  - Um disco na tecla com a versão USB 3.0 e posterior. O tamanho mínimo é de 4 GB.

  - Um ficheiro de imagem instalador ISO.

O disco numa chave será apagado neste processo.

Para preparar um disco numa chave:

1. Executar Rufus e selecionar **SENSOR ISO**.

1. Ligue o disco numa chave ao painel frontal.

1. Desapontina o BIOS do servidor a partir do USB.

## <a name="dell-poweredger340xl-installation"></a>Instalação Dell PowerEdgeR340XL

Antes de instalar o software no aparelho Dell, é necessário ajustar a configuração BIOS do aparelho:

  - [O Painel Frontal Dell PowerEdge R340](#dell-poweredge-r340-front-panel) e o [Painel Traseiro Dell PowerEdge R340](#dell-poweredge-r340-back-panel) contém a descrição dos painéis dianteiros e traseiros, juntamente com as informações necessárias para a instalação, tais como condutores e portas.

  - [A Configuração Dell BIOS](#dell-bios-configuration) fornece informações sobre como ligar-se à interface de gestão do aparelho Dell e configurar o BIOS.

  - [A Instalação de Software (Dell R340)](#software-installation-dell-r340) descreve o procedimento necessário para instalar o software de sensores IoT do Defender.

### <a name="dell-poweredge-r340xl-requirements"></a>Requisitos de Dell PowerEdge R340XL 

Para instalar o aparelho Dell PowerEdge R340XL, é necessário:

- Licença empresarial para Controlador de Acesso Remoto Dell (iDrac)

- Configuração BIOS XML

- Versões de firmware do servidor:

  - BioS versão 2.1.6

  - versão iDrac 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Painel frontal Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Painel frontal Dell PowerEdge R340.":::

 1. Painel de controlo esquerdo 
 1. Unidade ótica (opcional) 
 1. Painel de controlo direito 
 1. Etiqueta de informação 
 1. Unidades  

### <a name="dell-poweredge-r340-back-panel"></a>Painel traseiro de Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Painel traseiro de Dell PowerEdge R340.":::

1. Porta em série 
1. Porta NIC (Gb 1) 
1. Porta NIC (Gb 1) 
1. PCIe de meia altura 
1. Slot de cartão de expansão PCIe de altura completa 
1. Unidade de alimentação 1 
1. Unidade de alimentação 2 
1. Identificação do sistema 
1. Botão de porta de cabo indicador de estado do sistema (CMA) 
1. Porta USB 3.0 (2) 
1. porta de rede dedicada iDRAC9 
1. Porto VGA 

### <a name="dell-bios-configuration"></a>Configuração de DELL BIOS

A configuração Dell BIOS é necessária para ajustar o aparelho Dell para trabalhar com o software.

A configuração BIOS é executada através de uma configuração predefinida. O ficheiro está acessível a partir do [Centro de Ajuda.](https://help.cyberx-labs.com/)

Importe o ficheiro de configuração para o aparelho Dell. Antes de utilizar o ficheiro de configuração, é necessário estabelecer a comunicação entre o aparelho Dell e o computador de gestão.

O aparelho Dell é gerido por um iDRAC integrado com controlador de ciclo de vida (LC). O LC está incorporado em todos os servidores da Dell PowerEdge e fornece funcionalidades que o ajudam a implementar, atualizar, monitorizar e manter os seus aparelhos Dell PowerEdge.

Para estabelecer a comunicação entre o aparelho Dell e o computador de gestão, é necessário definir o endereço IP do iDRAC e o endereço IP do computador de gestão na mesma sub-rede.

Quando a ligação é estabelecida, o BIOS é configurável.

Para configurar a Dell BIOS:

1. [Configurar o endereço IP iDRAC](#configure-idrac-ip-address)

1. [Importar o ficheiro de configuração BIOS](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Configure endereço IP iDRAC

1. Ligue o sensor.

1. Se o SISTEMA já estiver instalado, selecione a tecla F2 para introduzir a configuração BIOS.

1. Selecione **configurações iDRAC**.

1. Selecione **Rede**.

   > [!NOTE]
   > Durante a instalação, deve configurar o endereço IP iDRAC predefinido e a palavra-passe mencionada nos seguintes passos. Após a instalação, altere estas definições.

1. Altere o endereço estático IPv4 para **10.100.100.250**.

1. Mude a máscara estática da sub-rede para **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Screenshot que mostra a máscara estática da sub-rede.":::

1. Selecione   >  **Back Finish**.

#### <a name="import-the-bios-configuration-file"></a>Importar o ficheiro de configuração BIOS

Este artigo descreve como configurar o BIOS utilizando o ficheiro de configuração.

1. Ligue um PC com um endereço IP pré-configurado estático **10.100.100.200** à porta **iDRAC.**

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Screenshot da porta de endereço IP pré-configurada.":::

1. Abra um browser e introduza **10.100.100.250** para ligar à interface web iDRAC.

1. Inscreva-se com os privilégios de administrador padrão da Dell:

   - Nome de utilizador: **raiz**

   - Senha: **calvin**

1. As credenciais do aparelho são:

   - Nome de utilizador: **XXX**

   - Senha: **XXX**

     A operação do perfil do servidor de importação é iniciada.

     > [!NOTE]
     > Antes de importar o ficheiro, certifique-se:
     > - É o único utilizador que está atualmente ligado ao iDRAC.
     > - O sistema não está no menu BIOS.

1. Aceda ao  >  **perfil de configuração do servidor**. Definir os seguintes parâmetros:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Screenshot que mostra a configuração do seu perfil de servidor.":::

   | Parâmetro | Configuração |
   |--|--|
   | Tipo de localização | Selecione **Local**. |
   | Caminho do arquivo | **Selecione Escolha O Ficheiro** e adicione o ficheiro XML de configuração. |
   | Componentes de Importação | Selecione **BIOS, NIC, RAID**. |
   | Tempo máximo de espera | Selecione **20 minutos**. |

1. Selecione **Import** (Importar).

1. Para monitorizar o processo, vá à   >  **Fila de Trabalho de** Manutenção.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Screenshot que mostra A Fila de Trabalho.":::

#### <a name="manually-configuring-bios"></a>BIOS configurar manualmente 

É necessário configurar manualmente o aparelho BIOS se:

- Não comprou o seu aparelho à Seta.

- Tem um aparelho, mas não tem acesso ao ficheiro de configuração XML.

Depois de aceder ao BIOS, aceda às **Definições do Dispositivo**.

Para configurar manualmente:

1. Aceda diretamente ao aparelho BIOS utilizando um teclado e ecrã ou utilize o iDRAC.

   - Se o aparelho não for um Defender para aparelho IoT, abra um browser e dirija-se ao endereço IP que já foi configurado anteriormente. Inscreva-se com os privilégios de administrador padrão da Dell. Utilize **raiz** para o nome de utilizador e **calvin** para obter a palavra-passe.

   - Se o aparelho for um Defender para aparelho IoT, inscreva-se utilizando **XXX** para o nome de utilizador e **XXX** para a senha.

1. Depois de aceder ao BIOS, aceda às **Definições do Dispositivo**.

1. Escolha a configuração controlada pelo RAID selecionando **o controlador RAID integrado 1: Utilitário de \<PERC H330 Adapter\> configuração Dell PERC**.

1. Selecione **Gestão de Configuração**.

1. Selecione **Criar Disco Virtual**.

1. No campo **Select RAID Level,** selecione **RAID5**. No campo **Nome do Disco Virtual,** insira **ROOT** e selecione **Discos Físicos**.

1. Selecione **Verificar Tudo** e, em seguida, selecione **Alterar Alterações**

1. Selecione **OK**.

1. Desloque-se para baixo e selecione **Criar Disco Virtual**.

1. Selecione a caixa de verificação **Confirmar** e selecione **Sim**.

1. Selecione **OK**.

1. Volte ao ecrã principal e selecione **System BIOS**.

1. Selecione **Configurações de arranque**.

1. Para a opção **Modo Boot,** selecione **BIOS**.

1. Selecione **Para trás** e, em seguida, **selecione Finish** para sair das definições BIOS.

### <a name="software-installation-dell-r340"></a>Instalação de software (Dell R340)

O processo de instalação demora cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

Para instalar:

1. Verifique se o suporte de versão está montado no aparelho de uma das seguintes formas:

   - Ligue o CD ou o disco externo numa chave com o desbloqueio.

   - Monte a imagem ISO utilizando o iDRAC. Depois de iniciar sessão no iDRAC, selecione a consola virtual e, em seguida, selecione **Virtual Media**.

1. Na secção **CD/DVD** do mapa, selecione **Escolha Ficheiro**.

1. Escolha o ficheiro de imagem ISO da versão para esta versão a partir da caixa de diálogo que abre.

1. Selecione o botão **dispositivo mapa.**

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Screenshot que mostra um dispositivo mapeado.":::

1. Os meios de comunicação estão montados. Selecione **Fechar**.

1. Ligue o aparelho. Quando estiver a utilizar o iDRAC, pode reiniciar os servidores selecionando o botão **Controlo do Cônsul.** Em seguida, no **Teclado Macros,** selecione o botão **Aplicar,** que iniciará a sequência Ctrl+Alt+Delete.

1. Selecione **Inglês**.

1. Selecione **SENSOR-RELEASE- \<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Selecione a versão do sensor e o tipo de empresa.":::   

1. Defina o perfil do aparelho e as propriedades da rede:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Screenshot que mostra o perfil do aparelho e as propriedades da rede.":::   

   | Parâmetro | Configuração |
   |--|--|
   | **Perfil de hardware** | **empresa** |
   | **Interface de gestão** | **eno1** |
   | **Parâmetros de rede (fornecidos pelo cliente)** | - |
   |**endereço IP da rede de gestão:** | - |
   | **máscara de sub-rede:** | - |
   | **Nome de hospedeiro do aparelho:** | - |
   | **DNS:** | - |
   | **endereço IP de gateway predefinido:** | - |
   | **interfaces de entrada:** |  O sistema gera a lista de interfaces de entrada para si. Para espelhar as interfaces de entrada, copie todos os itens apresentados na lista com um separador de vírgula. Não é preciso configurar a interface da ponte. Esta opção é utilizada apenas para casos de utilização especial. |

1. Após cerca de 10 minutos, os dois conjuntos de credenciais aparecem. Um é para um utilizador **CyberX,** e outro para um utilizador **de Suporte.**  

1. Guarde o ID do aparelho e as palavras-passe. Vai precisar destas credenciais para aceder à plataforma da primeira vez que a utilizar.

1. Selecione **Entrar** para continuar.

## <a name="hpe-proliant-dl20-installation"></a>Instalação HPE ProLiant DL20

Este artigo descreve o processo de instalação HPE ProLiant DL20, que inclui os seguintes passos:

  - Ativar o acesso remoto e atualizar a palavra-passe do administrador predefinido.
  - Configurar definições DE BIOS e RAID.
  - Instale o software.

### <a name="about-the-installation"></a>Sobre a instalação

  - Os aparelhos Enterprise e SMB podem ser instalados. O processo de instalação é idêntico para ambos os tipos de aparelhos, exceto para a configuração da matriz.
  - É fornecido um utilizador administrativo predefinido. Recomendamos que altere a palavra-passe durante o processo de configuração da rede.
  - Durante o processo de configuração da rede, irá configurar a porta da OIT na porta de rede 1.
  - O processo de instalação demora cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

### <a name="hpe-proliant-dl20-front-panel"></a>Painel frontal HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="Painel frontal HPE ProLiant DL20.":::

### <a name="hpe-proliant-dl20-back-panel"></a>Painel traseiro HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="O painel traseiro do HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Ativar o acesso remoto e atualizar a palavra-passe

Utilize o seguinte procedimento para configurar opções de rede e atualizar a palavra-passe predefinido.

Para ativar e atualizar a palavra-passe:

1. Ligue um ecrã e um teclado ao aparelho HP, ligue o aparelho e pressione **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Screenshot que mostra a janela HPE ProLiant.":::

1. Aceda às opções de rede de utilitários de configuração do sistema **de utilitários** de  >    >  **sistema de sistema oLO 5**  >  .

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Screenshot que mostra a janela de configuração do sistema.":::

    1.  Selecione **A Porta de Rede Partilhada Port-LOM** do campo **adaptador de interface de rede.**
    
    1.  Desative o DHCP.
    
    1.  Introduza o endereço IP, a máscara de sub-rede e o endereço IP gateway.

1. Selecione **F10: Guardar**.

1. Selecione **Esc** para voltar ao utilitário de **configuração do ILO 5** e, em seguida, selecione **Gestão do Utilizador**.

1. Selecione **Editar/Remova o utilizador.** O administrador é o único utilizador predefinido definido. 

1. Altere a palavra-passe predefinitiva e selecione **F10: Guardar**.

### <a name="configure-the-hpe-bios"></a>Configure o HPE BIOS

O procedimento a seguir descreve como configurar o HPE BIOS para os aparelhos da empresa e do SMB.

Para configurar o HPE BIOS:

1. Selecione **sistema utilitários**  >  **de configuração**  >  **BIOS/Configuração da plataforma (RBSU)**.

1. No formulário **BIOS/Configuração da Plataforma (RBSU),** selecione **Opções de Arranque**.

1. Mude **o modo boot** para o modo **BIOS legacy** e, em seguida, selecione **F10: Save**.

1. Selecione **Esc** duas vezes para fechar o formulário **de Configuração** do Sistema.

#### <a name="for-the-enterprise-appliance"></a>Para o aparelho da empresa

1. **Selecione Raid Incorporado 1: HPE Smart Array P408i-a SR Gen 10**  >  **Array Configuration**  >  **Create Array Array Array**.

1. No formulário **Criar Matriz,** selecione todas as opções. Estão disponíveis três opções para o aparelho **Enterprise.**

#### <a name="for-the-smb-appliance"></a>Para o aparelho SMB

1. **Selecione Raid Incorporado 1: HPE Smart Array P208i-a SR Gen 10**  >  **Array Configuration**  >  **Create Array Array Array**.

1. **Selecione Proceder para o próximo formulário**.

1. No formulário **De Nível RAID definido,** desa um nível para **RAID 5** para implementações de empresas e **RAID 1** para implementações SMB.

1. **Selecione Proceder para o próximo formulário**.

1. No formulário **Lógico Drive Label,** **introduza a Unidade Lógica 1**.

1. Selecione **Alterars de submissão**.

1. No formulário **Enviar,** selecione **Voltar para o Menu Principal.**

1. Selecione **F10: Guarde** e, em seguida, prima **Esc duas** vezes.

1. Na janela **System Utilities,** selecione **o Menu de Arranque one-Time**.

1. No formulário **one-time Boot Menu,** selecione **Legacy BIOS One-Time Boot Menu**.

1. Aparecem as janelas **de Arranque em Legacy** e Boot **Override.** Escolha uma opção de substituição de botas; por exemplo, para uma casca de CD-ROM, USB, HDD ou UEFI.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Screenshot que mostra a primeira janela de override boot.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Screenshot que mostra a segunda janela de override boot.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Instalação de software (aparelho HPE ProLiant DL20)

O processo de instalação demora cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

Para instalar o software:

1. Ligue o ecrã e o teclado ao aparelho e, em seguida, ligue-o ao CLI.

1. Conecte um CD ou disco externo na chave com a imagem ISO que descarregou a partir da página **'Actualizações'** no portal Defender para IoT.

1. Ligue o aparelho.

1. Selecione **Inglês**.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Seleção de inglês na janela do CLI.":::

1. Selecione **SENSOR-RELEASE- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Screenshot do ecrã para selecionar uma versão.":::

1. No Assistente de Instalação define o perfil de hardware e as propriedades da rede:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Screenshot que mostra o Assistente de Instalação.":::

    | Parâmetro | Configuração |
    | ----------| ------------- |
    | **Perfil de hardware** | Selecione **Enterprise** ou **Office** para implementações SMB. |
    | **Interface de gestão** | **eno2** |
    | **Parâmetros de rede predefinidos (normalmente os parâmetros são fornecidos pelo cliente)** | **endereço IP da rede de gestão:** <br/> <br/>**Nome de hospedeiro do aparelho:** <br/>**DNS:** <br/>**o endereço IP do gateway predefinido:**|
    | **interfaces de entrada:** | O sistema gera a lista de interfaces de entrada para si.<br/><br/>Para espelhar as interfaces de entrada, copie todos os itens apresentados na lista com um separador de vírgula: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**Para HPE DL20: Não liste eno1, enp1s0f4u4 (interfaces iLo)**<br/><br/>**PONTE:** Não há necessidade de configurar a interface da ponte. Esta opção é utilizada apenas para casos de utilização especial. Prima **Enter** para continuar. |

1. Após cerca de 10 minutos, os dois conjuntos de credenciais aparecem. Um é para um utilizador **CyberX,** e outro para um utilizador **de Suporte.**

1. Guarde a identificação e as palavras-passe do aparelho. Vai precisar das credenciais para aceder à plataforma pela primeira vez.

1. Selecione **Entrar** para continuar.

## <a name="hpe-proliant-dl360-installation"></a>Instalação HPE ProLiant DL360

  - É fornecido um utilizador administrativo predefinido. Recomendamos que altere a palavra-passe durante a configuração da rede.

  - Durante a configuração da rede, irá configurar a porta da OIT.

  - O processo de instalação demora cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

### <a name="hpe-proliant-dl360-front-panel"></a>Painel frontal HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="Painel frontal HPE ProLiant DL360.":::

### <a name="hpe-proliant-dl360-back-panel"></a>Painel traseiro HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="Painel traseiro HPE ProLiant DL360.":::

### <a name="enable-remote-access-and-update-the-password"></a>Ativar o acesso remoto e atualizar a palavra-passe

Consulte as secções anteriores para a instalação HPE ProLiant DL20:

  - "Ativar o acesso remoto e atualizar a palavra-passe"

  - "Configurar o HPE BIOS"

A configuração da empresa é idêntica.

> [!Note]
> No formulário de matriz, verifique se seleciona todas as opções.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>instalação remota da OIT (a partir de uma unidade virtual)

Este procedimento descreve a instalação da OIT a partir de uma unidade virtual.

Para instalar:

1. Inscreva-se na consola da OIT e, em seguida, clique no ecrã dos servidores.

1. Selecione **a consola HTML5**.

1. Na consola, selecione o ícone de CD e escolha a opção CD/DVD.

1. Selecione **o ficheiro ISO local.**

1. Na caixa de diálogo, escolha o ficheiro ISO relevante.

1. Vá para o ícone esquerdo, selecione **Power** e selecione **Reset**.

1. O aparelho reiniciará e executará o processo de instalação do sensor.

### <a name="software-installation-hpe-dl360"></a>Instalação de software (HPE DL360)

O processo de instalação demora cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

Para instalar:

1. Ligue o ecrã e o teclado ao aparelho e, em seguida, ligue-o ao CLI.

1. Conecte um CD ou disco externo numa chave com a imagem ISO que descarregou a partir da página **'Actualizações'** no portal Defender para IoT.

1. Ligue o aparelho.

1. Selecione **Inglês**.

1. Selecione **SENSOR-RELEASE- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Screenshot que mostra a seleção da versão.":::

1. No Assistente de Instalação, defina o perfil do aparelho e as propriedades da rede.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Screenshot que mostra o Assistente de Instalação.":::

    | Parâmetro | Configuração |
    | ----------| ------------- |
    | **Perfil de hardware** | Selecione **corporate**. |
    | **Interface de gestão** | **eno2** |
    | **Parâmetros de rede predefinidos (fornecidos pelo cliente)** | **endereço IP da rede de gestão:** <br/>**máscara de sub-rede:** <br/>**Nome de hospedeiro do aparelho:** <br/>**DNS:** <br/>**o endereço IP do gateway predefinido:**|
    | **interfaces de entrada:**  | O sistema gera uma lista de interfaces de entrada para si.<br/><br/>Para espelhar as interfaces de entrada, copie todos os itens apresentados na lista com um separador de vírgula.<br/><br/> Não é necessário configurar a interface da ponte. Esta opção é utilizada apenas para casos de utilização especial. |

1. Após cerca de 10 minutos, os dois conjuntos de credenciais aparecem. Um é para um utilizador **CyberX,** e outro para um utilizador **de suporte.**

1. Guarde a identificação e as palavras-passe do aparelho. Vai precisar destas credenciais para aceder à plataforma pela primeira vez.

1. Selecione **Entrar** para continuar.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Instalação de sensores para o aparelho virtual

Pode implantar a máquina virtual para o sensor Defender para IoT nas seguintes arquiteturas:


| Arquitetura | Especificações | Utilização | Comentários |
|---|---|---|---|
| **Enterprise** | CPU: 8<br/>Memória: 32G RAM<br/>HDD: 1800 GB | Ambiente de produção | Padrão e mais comum |
| **Small Business** | CPU: 4 <br/>Memória: 8G RAM<br/>HDD: 500 GB | Ensaio ou pequenos ambientes de produção | -  |
| **Office** | CPU: 4<br/>Memória: 8G RAM<br/>HDD: 100 GB | Pequenos ambientes de teste | -  |

### <a name="prerequisites"></a>Pré-requisitos

A consola de gestão no local suporta opções de implementação VMware e Hyper-V. Antes de iniciar a instalação, certifique-se de que tem os seguintes itens:

  - VMware (ESXi 5.5 ou posterior) ou Hipervisor Hiper-V (Windows 10 Pro ou Enterprise) instalado e operacional

  - Recursos de hardware disponíveis para a máquina virtual

  - Ficheiro de instalação ISO para o Azure Defender para sensor IoT

Certifique-se de que o hipervisor está a funcionar.

### <a name="create-the-virtual-machine-esxi"></a>Criar a máquina virtual (ESXi)

1. Inscreva-se no ESXi, escolha a **datastore** relevante e selecione **Datastore Browser**.

1. **Faça** o upload da imagem e **selecione Close**.

1. Vá a **Máquinas Virtuais** e, em seguida, selecione **Criar/Registar VM**.

1. Selecione **Criar uma nova máquina virtual** e, em seguida, selecione **Seguinte**.

1. Adicione um nome de sensor e escolha:

   - Compatibilidade: **&lt; versão mais recente &gt; do ESXi**

   - Família de SO convidado: **Linux**

   - Versão so convidado: **Ubuntu Linux (64-bit)**

1. Selecione **Seguinte**.

1. Escolha a loja de dados relevante e selecione **Next**.

1. Altere os parâmetros de hardware virtuais de acordo com a arquitetura necessária.

1. Para **o CD/DVD Drive 1**, selecione **o ficheiro ISO da Datastore** e escolha o ficheiro ISO que fez o upload anterior.

1. Selecione **Seguinte** > **Concluir**.

### <a name="create-the-virtual-machine-hyper-v"></a>Criar a máquina virtual (Hiper-V)

Este procedimento descreve como criar uma máquina virtual utilizando o Hyper-V.

Para criar uma máquina virtual:

1. Crie um disco virtual no Hyper-V Manager.

1. Selecione **o formato = VHDX**.

1. Selecione **tipo = Expansão Dinâmica.**

1. Insira o nome e a localização do VHD.

1. Introduza o tamanho exigido (de acordo com a arquitetura).   

1. Reveja o resumo e **selecione Acabamento**.

1. No menu **Ações,** crie uma nova máquina virtual.

1. Insira um nome para a máquina virtual.

1. **Selecione Especificar**  >  **Geração 1**.

1. Especifique a atribuição de memória (de acordo com a arquitetura) e selecione a caixa de verificação para memória dinâmica.

1. Configure o adaptador de rede de acordo com a topologia da rede do servidor.

1. Ligue o VHDX criado anteriormente à máquina virtual.

1. Reveja o resumo e **selecione Acabamento**.

1. Clique com o botão direito na nova máquina virtual e selecione **Definições**.

1. **Selecione Adicionar Hardware** e adicione um novo adaptador de rede.

1. Selecione o interruptor virtual que irá ligar-se à rede de gestão de sensores.

1. Alocar recursos da CPU (de acordo com a arquitetura).

1. Ligue a imagem ISO da consola de gestão a uma unidade virtual de DVD.

1. Inicie a máquina virtual.

2. No menu **Ações,** selecione **Connect** para continuar a instalação do software.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalação de software (ESXi e Hyper-V)

Esta secção descreve a instalação de software ESXi e Hyper-V.

Para instalar:

1. Abra a consola de máquinas virtuais.

1. O VM começará a partir da imagem ISO, e o ecrã de seleção de idiomas aparecerá. Selecione **Inglês**.

1. Selecione a arquitetura necessária.

1. Defina o perfil do aparelho e as propriedades da rede:

    | Parâmetro | Configuração |
    | ----------| ------------- |
    | **Perfil de hardware** | &lt;arquitetura necessária&gt; |
    | **Interface de gestão** | **ens192** |
    | **Parâmetros de rede (fornecidos pelo cliente)** | **endereço IP da rede de gestão:** <br/>**máscara de sub-rede:** <br/>**Nome de hospedeiro do aparelho:** <br/>**DNS:** <br/>**gateway padrão:** <br/>**interfaces de entrada:**|
    | **interfaces de ponte:** | Não há necessidade de configurar a interface da ponte. Esta opção destina-se apenas a casos de utilização especial. |

1. Insira **Y** para aceitar as definições.

1. As credenciais de inscrição são geradas e apresentadas automaticamente. Copie o nome de utilizador e a palavra-passe num local seguro, porque são necessários para iniciar seduca e administração.

      - **Suporte**: Utilizador administrativo para gestão de utilizadores.

      - **CyberX**: O equivalente à raiz para aceder ao aparelho.

1. O aparelho reinicia.

1. Aceda à consola de gestão através do endereço IP previamente configurado: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Screenshot que mostra acesso à consola de gestão.":::

## <a name="on-premises-management-console-installation"></a>Instalação de consolas de gestão no local

Antes de instalar o software no aparelho, é necessário ajustar a configuração BIOS do aparelho:

### <a name="bios-configuration"></a>Configuração BIOS

Para configurar o BIOS para o seu aparelho:

1. [Ativar o acesso remoto e atualizar a palavra-passe](#enable-remote-access-and-update-the-password).

1. [Configure o BIOS](#configure-the-hpe-bios).

### <a name="software-installation"></a>Instalação de software

O processo de instalação demora cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes. 

Durante o processo de instalação, poderá adicionar um NIC secundário. Se optar por não instalar o NIC secundário durante a instalação, pode [adicionar um NIC secundário](#add-a-secondary-nic) mais tarde. 

Para instalar o software:

1. Selecione o seu idioma preferido para o processo de instalação.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="Selecione o seu idioma preferido para o processo de instalação.":::     

1. Selecione **MANAGEMENT-RELEASE- \<version\> \<deployment type\>**.

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="Selecione a sua versão.":::   

1. No Assistente de Instalação, defina as propriedades da rede:

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="Screenshot que mostra o perfil do aparelho.":::   

   | Parâmetro | Configuração |
   |--|--|
   | **configure interface de rede de gestão de gestão** | Para Dell: **eth0, eth1** <br /> Para HP: **enu1, enu2** <br /> ou <br />**valor possível** |
   | **configure endereço IP da rede de gestão:** | **Endereço IP fornecido pelo cliente** |
   | **máscara de sub-rede configuração:** | **Endereço IP fornecido pelo cliente** |
   | **configurar DNS:** | **Endereço IP fornecido pelo cliente** |
   | **configurar o endereço IP de gateway predefinido:** | **Endereço IP fornecido pelo cliente** |
   
1. **(Opcional)** Se pretender instalar um Cartão de Interface de Rede Secundária (NIC), defina o seguinte perfil do aparelho e as propriedades da rede:

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="Screenshot que mostra as perguntas de instalação do NIC secundário.":::

   | Parâmetro | Configuração |
   |--|--|
   | **configure a interface de monitorização do sensor (Opcional):** | **eth1**, ou **valor possível** |
   | **configurar um endereço IP para a interface de monitorização do sensor:** | **Endereço IP fornecido pelo cliente** |
   | **configurar uma máscara de sub-rede para a interface de monitorização do sensor:** | **Endereço IP fornecido pelo cliente** |

1. Aceite os assentamentos e continue grafando `Y` . 

1. Após cerca de 10 minutos, os dois conjuntos de credenciais aparecem. Um é para um utilizador **CyberX,** e outro para um utilizador **de Suporte.**

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="Copie estas credenciais pois não serão apresentadas novamente.":::  

   Guarde os nomes de utilizador e as palavras-passe, vai precisar destas credenciais para aceder à plataforma na primeira vez que a utilizar.

1. Selecione **Entrar** para continuar.

Para obter informações sobre como encontrar a porta física no seu aparelho, consulte [encontre a sua porta](#find-your-port).

### <a name="add-a-secondary-nic"></a>Adicione um NIC secundário

Pode aumentar a segurança da sua consola de gestão no local adicionando um NIC secundário. Ao adicionar um NIC secundário terá um dedicado aos seus utilizadores, e o outro irá suportar a configuração de um gateway para redes routed. O segundo NIC é dedicado a todos os sensores anexados dentro de um intervalo de endereços IP.

Ambos os NICs têm a interface de utilizador (UI) ativada. Quando o encaminhamento não for necessário, todas as funcionalidades que são suportadas pela UI estarão disponíveis no NIC secundário. A Alta Disponibilidade será executada no NIC secundário.

Se optar por não implementar um NIC secundário, todas as funcionalidades estarão disponíveis através do NIC primário. 

Se já configurar a sua consola de gestão no local e gostaria de adicionar um NIC secundário à sua consola de gestão no local, utilize os seguintes passos:

1. Utilize o comando de reconfiguração da rede:

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. Introduza as seguintes respostas às seguintes questões:

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="Introduza as seguintes respostas para configurar o seu aparelho.":::

    | Parâmetros | Resposta para entrar |
    |--|--|
    | **Endereço IP da Rede de Gestão** | `N` |
    | **Máscara de sub-rede** | `N` |
    | **DNS** | `N` |
    | **Endereço IP de gateway predefinido** | `N` |
    | **Interface de monitorização do sensor (Opcional. Aplicável quando os sensores estão num segmento de rede diferente. Para mais informações, consulte as instruções de instalação)**| `Y`, **selecionar um valor possível** |
    | **Um endereço IP para a interface de monitorização do sensor (acessível pelos sensores)** | `Y`, **endereço IP fornecido pelo cliente**|
    | **Uma máscara de sub-rede para a interface de monitorização do sensor (acessível pelos sensores)** | `Y`, **endereço IP fornecido pelo cliente** |
    | **Hostname (Nome do anfitrião)** | **fornecida pelo cliente** |

1. Reveja todas as escolhas e entre `Y` para aceitar as alterações. O sistema reinicia.

### <a name="find-your-port"></a>Encontre o seu porto

Se tiver problemas em localizar a porta física no seu dispositivo, pode utilizar o seguinte comando para:

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

Este comando fará com que a luz da porta pise durante o período de tempo especificado. Por exemplo, `sudo ethtool -p eno1 120` entrando, terá flash eno1 da porta durante 2 minutos, permitindo-lhe encontrar a porta na parte de trás do seu aparelho. 

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Aparelho virtual: Instalação de consola de gestão no local

A consola de gestão no local, VM, suporta as seguintes arquiteturas:

| Arquitetura | Especificações | Utilização | 
|--|--|--|
| Grandes Empresas <br/>(Predefinido e mais comum) | CPU: 8 <br/>Memória: 32G RAM<br/> HDD: 1.8 TB | Grandes ambientes produtivos | 
| Grandes Empresas | CPU: 4 <br/> Memória: 8G RAM<br/> HDD: 500 GB | Grandes ambientes produtivos |
| Grandes Empresas | CPU: 4 <br/>Memória: 8G RAM <br/> HDD: 100 GB | Pequenos ambientes de teste | 
   
### <a name="prerequisites"></a>Pré-requisitos

A consola de gestão no local suporta opções de implementação VMware e Hyper-V. Antes de iniciar a instalação, verifique o seguinte:

- O hipervisor VMware (ESXi 5.5 ou posterior) ou hipervisor Hiper-V (Windows 10 Pro ou Enterprise) está instalado e operacional.

- Os recursos de hardware estão disponíveis para a máquina virtual.

- Tem o ficheiro de instalação ISO para a consola de gestão no local.
    
- O hipervisor está a funcionar.

### <a name="create-the-virtual-machine-esxi"></a>Criar a máquina virtual (ESXi)

Para criar uma máquina virtual (ESXi):

1. Inscreva-se no ESXi, escolha a **datastore** relevante e selecione **Datastore Browser**.

1. Faça o upload da imagem e **selecione Close**.

1. Ir a **Máquinas Virtuais.**

1. Selecione **Criar/Registar VM**.

1. Selecione **Criar uma nova máquina virtual** e selecione **Seguinte**.

1. Adicione um nome de sensor e escolha:

   - Compatibilidade: \<latest ESXi version>

   - Família de SO convidado: Linux

   - Versão so convidado: Ubuntu Linux (64-bit)

1. Selecione **Seguinte**.

1. Escolha a loja de dados relevante e selecione **Next**.

1. Altere os parâmetros de hardware virtuais de acordo com a arquitetura necessária.

1. Para **o CD/DVD Drive 1**, selecione **o ficheiro ISO da Datastore** e escolha o ficheiro ISO que fez o upload anterior.

1. Selecione **Seguinte** > **Concluir**.

### <a name="create-the-virtual-machine-hyper-v"></a>Criar a máquina virtual (Hiper-V)

Para criar uma máquina virtual utilizando o Hyper-V:

1. Crie um disco virtual no Hyper-V Manager.

1. Selecione o formato **VHDX**.

1. Selecione **Seguinte**.

1. Selecione o tipo **de expansão Dinâmica**.

1. Selecione **Seguinte**.

1. Insira o nome e a localização do VHD.

1. Selecione **Seguinte**.

1. Introduza o tamanho exigido (de acordo com a arquitetura).

1. Selecione **Seguinte**.

1. Reveja o resumo e **selecione Acabamento**.

1. No menu **Ações,** crie uma nova máquina virtual.

1. Selecione **Seguinte**.

1. Insira um nome para a máquina virtual.

1. Selecione **Seguinte**.

1. Selecione **Generation** e coloque-a na **Geração 1**.

1. Selecione **Seguinte**.

1. Especifique a atribuição de memória (de acordo com a arquitetura) e selecione a caixa de verificação para memória dinâmica.

1. Selecione **Seguinte**.

1. Configure o adaptador de rede de acordo com a topologia da rede do servidor.

1. Selecione **Seguinte**.

1. Ligue o VHDX criado anteriormente à máquina virtual.

1. Selecione **Seguinte**.

1. Reveja o resumo e **selecione Acabamento**.

1. Clique com o botão direito na nova máquina virtual e, em seguida, selecione **Definições**.

1. **Selecione Adicionar Hardware** e adicione um novo adaptador para **adaptador de rede**.

1. Para **a Switch Virtual**, selecione o interruptor que irá ligar-se à rede de gestão de sensores.

1. Alocar recursos da CPU (de acordo com a arquitetura).

1. Ligue a imagem ISO da consola de gestão a uma unidade virtual de DVD.

1. Inicie a máquina virtual.

1. No menu **Ações,** selecione **Connect** para continuar a instalação do software.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalação de software (ESXi e Hyper-V)

O arranque da máquina virtual iniciará o processo de instalação a partir da imagem ISO.

Para instalar o software:

1. Selecione **Inglês**.

1. Selecione a arquitetura necessária para a sua implantação.

1. Defina a interface de rede para a rede de gestão de sensores: interface, IP, sub-rede, servidor DNS e gateway predefinido.

1. As credenciais de inscrição são geradas automaticamente. Guarde o nome de utilizador e as palavras-passe, vai precisar destas credenciais para aceder à plataforma na primeira vez que a utilizar.

   Em seguida, o aparelho reiniciará.

1. Aceda à consola de gestão através do endereço IP previamente configurado: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Screenshot que mostra o ecrã de insuição da consola de gestão.":::

## <a name="post-installation-validation"></a>Validação pós-instalação

Para validar a instalação de um aparelho físico, é necessário realizar muitos testes. O mesmo processo de validação aplica-se a todos os tipos de aparelhos.

Efetuar a validação utilizando o GUI ou o CLI. A validação está disponível para o **suporte** ao utilizador e para o utilizador **CyberX.**

A validação pós-instalação deve incluir os seguintes ensaios:

  - **Teste de sanidade**: Verifique se o sistema está em funcionamento.

  - **Versão**: Verifique se a versão está correta.

  - **se configurar**: Verifique se todas as interfaces de entrada configuradas durante o processo de instalação estão em execução.

### <a name="checking-system-health-by-using-the-gui"></a>Verificação da saúde do sistema utilizando o GUI

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Screenshot que mostra o exame de saúde do sistema.":::

#### <a name="sanity"></a>Sanidade

- **Aparelho**: Verifique a sanidade do aparelho. Pode efetuar a mesma verificação utilizando o comando CLI `system-sanity` .

- **Versão**: Mostra a versão do aparelho.

- **Propriedades da rede**: Exibe os parâmetros da rede do sensor.

#### <a name="redis"></a>Redis

- **Memória**: Fornece a imagem geral do uso da memória, como a quantidade de memória utilizada e quanto permaneceu.

- **Tecla mais longa**: Apresenta as teclas mais compridas que podem causar uma utilização extensiva da memória.

#### <a name="system"></a>Sistema

- **Core Log**: Fornece as últimas 500 linhas do registo principal, permitindo-lhe visualizar as recentes linhas de registo sem exportar todo o registo do sistema.

- **Gestor de tarefas**: Traduz as tarefas que aparecem na tabela de processos para as seguintes camadas: 
  
  - Camada persistente (Redis) 
  - Camada de caixa (SQL)

- **Estatísticas da rede**: Apresenta as suas estatísticas de rede.

- **TOP**: Mostra a tabela de processos. É um comando Linux que proporciona uma visão dinâmica em tempo real do sistema de execução.

- **Verificação da memória de reserva**: Fornece o estado da memória de reserva, verificando o seguinte:
  - A localização da pasta de reserva 
  - O tamanho da pasta de reserva
  - As limitações da pasta de reserva
  - Quando o último reforço aconteceu
  - Quanto espaço há para os ficheiros de backup extra

- **seconfig**: Apresenta os parâmetros para as interfaces físicas do aparelho.

- **CyberX nload**: Exibe tráfego de rede e largura de banda utilizando os testes de seis segundos.

- **Erros do Core, log**: Apresenta erros do ficheiro de registo principal.

Para aceder à ferramenta:

1. Inscreva-se no sensor com as credenciais do utilizador **Do Suporte.**

1. Selecione estatísticas do **sistema** a partir da janela Definições do **Sistema.**

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Verificação da saúde do sistema utilizando o CLI

**Teste 1: Sanidade**

Verifique se o sistema está a funcionar:

1. Ligue-se ao CLI com o terminal Linux (por exemplo, PuTTY) e o **suporte** ao utilizador.

1. Introduza `system sanity`.

1. Verifique se todos os serviços estão verdes (em funcionamento).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Screenshot que mostra serviços de execução.":::

1. Verifique se **o Sistema está UP! (prod)** aparece na parte inferior.

**Teste 2: Verificação da versão**

Verifique se a versão correta é utilizada:

1. Ligue-se ao CLI com o terminal Linux (por exemplo, PuTTY) e o **suporte** ao utilizador.

1. Introduza `system version`.

1. Verifique se a versão correta aparece.

**Teste 3: Validação da rede**

Verifique se todas as interfaces de entrada configuradas durante o processo de instalação estão em execução:

1. Ligue-se ao CLI com o terminal Linux (por exemplo, PuTTY) e o **suporte** ao utilizador.

1. Introduza `network list` (o equivalente ao comando Linux). `ifconfig`

1. Validar que aparecem as interfaces de entrada necessárias. Por exemplo, se forem instalados dois NICs quad copper, devem existir 10 interfaces na lista.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Screenshot que mostra a lista de interfaces.":::

**Teste 4: Acesso de gestão à UI**

Verifique se pode aceder à web da consola GUI:

1. Ligue um portátil com um cabo Ethernet à porta de gestão **(Gb1**).

1. Defina o endereço NIC do portátil para estar na mesma gama que o aparelho.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Screenshot que mostra acesso de gestão à UI.":::

1. Pingar o endereço IP do aparelho do portátil para verificar a conectividade (predefinição: 10.100.10.1).

1. Abra o navegador Chrome no portátil e introduza o endereço IP do aparelho.

1. Na **sua ligação não é** uma janela privada, selecione **Advanced** e prossiga.

1. O teste é bem sucedido quando o ecrã de inscrição do Defender para IoT aparece.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Screenshot que mostra acesso à consola de gestão.":::

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="you-cant-connect-by-using-a-web-interface"></a>Não é possível ligar usando uma interface web

1. Verifique se o computador que está a tentar ligar está na mesma rede que o aparelho.

1. Verifique se a rede GUI está ligada à porta de gestão.

1. A verificar o endereço IP do aparelho. Se não houver ping:

   1. Ligue um monitor e um teclado ao aparelho.

   1. Utilize o utilizador **de Suporte** e a palavra-passe para iniciar sinsus.

   1. Utilize o comando `network list` para ver o endereço IP atual.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Screenshot que mostra a lista de rede.":::

1. Se os parâmetros da rede estiverem mal configurados, utilize o seguinte procedimento para os alterar:

   1. Utilize o `network edit-settings` comando.

   1. Para alterar o endereço IP da rede de gestão, selecione **Y**.

   1. Para alterar a máscara da sub-rede, selecione **Y**.

   1. Para alterar o DNS, selecione **Y**.

   1. Para alterar o endereço IP de gateway predefinido, selecione **Y**.

   1. Para a alteração da interface de entrada (apenas no sensor), selecione **N**.

   1. Para aplicar as definições, selecione **Y**.

1. Após o reinício, ligue-se às credenciais do utilizador de suporte e utilize o `network list` comando para verificar se os parâmetros foram alterados.

1. Tente ping e ligue-se do GUI novamente.

### <a name="the-appliance-isnt-responding"></a>O aparelho não está a responder.

1. Ligue um monitor e um teclado ao aparelho ou utilize o PuTTY para ligar remotamente ao CLI.

1. Utilize as credenciais do utilizador **do Suporte** para iniciar sinsus.

1. Use o `system sanity` comando e verifique se todos os processos estão em curso.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Screenshot que mostra o comando de sanidade do sistema.":::

Para qualquer outro problema, contacte [o Microsoft Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Apêndice A: Porta de espelhamento em vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Configure uma porta SPAN em um vSwitch existente

Um vSwitch não tem capacidades de espelhamento, mas pode utilizar uma solução alternativa para implementar uma porta SPAN.

Para configurar uma porta SPAN:

1. Abra as propriedades vSwitch.

1. Selecione **Adicionar**.

1. Selecione **máquina virtual**  >  **seguinte**.

1. Insira uma **rede de etiquetas de** rede SPAN Network , selecione **VLAN ID**  >  **All** e, em seguida, selecione **Next**.

1. Selecione **Concluir**.

1. Selecione **> de rede SPAN** **Editar*.

1. Selecione **Segurança** e verifique se a política **do Modo Promíscuo** está definida para o modo **Aceitar.**

1. Selecione **OK** e, em seguida, selecione **Close** para fechar as propriedades vSwitch.

1. Abra as propriedades **XSense VM.**

1. Para **adaptador de rede 2**, selecione a rede **SPAN.**

1. Selecione **OK**.

1. Ligue ao sensor e verifique se o espelhamento funciona.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Apêndice B: Sensores de acesso a partir da consola de gestão no local

Pode aumentar a segurança do sistema evitando o acesso direto do utilizador ao sensor. Em vez disso, utilize um túnel de procuração para permitir que os utilizadores acedam ao sensor a partir da consola de gestão no local com uma única regra de firewall. Esta técnica reduz a possibilidade de acesso não autorizado ao ambiente de rede para além do sensor. A experiência do utilizador ao iniciar sessão no sensor permanece a mesma.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Screenshot que mostra acesso ao sensor.":::

Para permitir a escavação de túneis:

1. Inscreva-se no CLI da consola de gestão no local com credenciais de utilizador **CyberX** ou **Support.**

1. Introduza `sudo cyberx-management-tunnel-enable`.

1. Selecione **Enter** (Introduzir).

1. Introduza `--port 10000`.

### <a name="next-steps"></a>Passos seguintes

[Configurar a sua rede](how-to-set-up-your-network.md)