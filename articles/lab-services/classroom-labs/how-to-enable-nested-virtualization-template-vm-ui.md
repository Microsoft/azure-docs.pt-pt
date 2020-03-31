---
title: Ativar a virtualização aninhada num modelo VM em Serviços de Laboratório Azure (UI) [ Microsoft Docs
description: Aprenda a criar um Modelo VM com vários VMs no interior.  Por outras palavras, permita a virtualização aninhada num modelo VM nos Serviços de Laboratório Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 10f8d2760474631fbcabac4d66139aedf4c7560c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503078"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Ativar a virtualização aninhada em uma máquina virtual modelo em Azure Lab Services manualmente

A virtualização aninhada permite criar um ambiente multi-VM dentro da máquina virtual modelo de um laboratório. A publicação do modelo fornecerá a cada utilizador do laboratório uma máquina virtual configurada com vários VMs dentro dele.  Para obter mais informações sobre a virtualização aninhada e os serviços de laboratório do Azure, consulte [Enable aninhadamente virtualização em uma máquina virtual modelo em Serviços de Laboratório Azure](how-to-enable-nested-virtualization-template-vm.md).

Este artigo abrange como configurar a virtualização aninhada numa máquina de modelos em Serviços de Laboratório Azure utilizando diretamente papéis e ferramentas do Windows.  Há algumas coisas necessárias para permitir que uma classe use virtualização aninhada.  Os passos abaixo descreverão como configurar manualmente um modelo de máquina de Serviços de Laboratório com Hyper-V.  Os passos destinam-se ao Windows Server 2016 ou ao Windows Server 2019.  

>[!IMPORTANT]
>Selecione **Large (virtualização aninhada)** ou **Média (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  A virtualização aninhada não funcionará de outra forma.  

## <a name="enable-hyper-v-role"></a>Ativar o papel hyper-V

Os seguintes passos descrevem as ações necessárias para permitir o Hyper-V no Servidor do Windows utilizando qualquer um dos Gestores de Servidores.  Assim que a instalação for bem sucedida, o gestor hyper-V estará disponível para adicionar, modificar e eliminar máquinas virtuais do cliente.

1. No **Gestor do Servidor**, na página do Dashboard, clique em Adicionar **Funções e Funcionalidades**.
2. Na página **Antes de começar**, clique em **Seguinte**.
3. Na página do tipo de **instalação Select,** mantenha a seleção predefinida da instalação baseada em funções ou baseada em funções e, em seguida, clique em **Next**.
4. Na página do servidor de **destino Select,** selecione Selecione um servidor a partir da piscina do servidor.   O servidor atual já será selecionado.  Clique em Seguinte.
5. Na página de funções do **servidor Select,** selecione **Hyper-V**.  
6. Aparecerão os pop-ups **Add Roles and Features Wizard.**  Selecione Incluir ferramentas de **gestão (se aplicável)**.  Clique no botão **Adicionar Funcionalidades.**
7. Na página **Selecionar papeis de servidor**, clique em **Seguinte**.
8. Na **página 'Seleccionar'** **Next**
9. Na página **Hyper-V**, clique em **Seguinte**.
10. Na página **Create Virtual Switches,** aceite as predefinições e clique **em Seguinte**.
11. Na página de Migração de **Máquinas Virtuais,** aceite os predefinições e clique **em Seguinte**.
12. Na página **'Lojas Predefinidas',** aceite as predefinições e clique em **Next**.
13. Na página de seleções de **instalações Confirmar,** selecione **Reiniciar automaticamente o servidor de destino se necessário**.
14. Quando aparecer o pop-up **add roles and features Wizard,** clique em **Sim**.
15. Clique em **Instalar**.
16. Aguarde a página de progresso da **instalação** para indicar que a função Hyper-V está completa.  A máquina pode reiniciar no meio da instalação.
17. Clique em **Fechar**.

## <a name="enable-dhcp-role"></a>Ativar o papel dHCP

Quaisquer máquinas virtuais de clientes Hiper-V criadas, precisam de um endereço IP na rede NAT.  Criaremos a rede NAT mais tarde.  Uma forma de atribuir endereços IP é configurar o hospedeiro, neste caso o modelo de máquina virtual do laboratório, como servidor DHCP.  Abaixo estão os passos necessários para ativar a função DHCP.

1. No **Gestor do Servidor**, na página do **Dashboard,** clique em **Adicionar Funções e Funcionalidades**.
2. Na página **Antes de começar**, clique em **Seguinte**.
3. Na página do tipo de **instalação Select,** selecione **instalação baseada em funções ou baseada em funções** e, em seguida, clique em **Next**.
4. Na página do servidor de **destino Select,** selecione o servidor atual a partir da piscina do servidor e, em seguida, clique **em Next**.
5. Na página de funções do **servidor Select,** selecione **DHCP Server**.  
6. Aparecerão os pop-ups **Add Roles and Features Wizard.**  Selecione Incluir ferramentas de **gestão (se aplicável)**.  Clique em **adicionar funcionalidades**.

    >[!NOTE]
    >Pode ver um erro de validação indicando que não foram encontrados endereços IP estáticos.  Este aviso pode ser ignorado para o nosso cenário.

7. Na página **Selecionar papeis de servidor**, clique em **Seguinte**.
8. Na página **'Seleccionar'** **Next**
9. Na página **do Servidor DHCP,** clique em **Seguinte**.
10. Na página **Confirmar seleções de instalação**, clique em **Instalar**.
11. Aguarde a página de progresso da **instalação** para indicar que a função DHCP está completa.
12. Clique em Fechar.

## <a name="enable-routing-and-remote-access-role"></a>Ativar a função de encaminhamento e acesso remoto

1. No **Gestor do Servidor**, na página do **Dashboard,** clique em **Adicionar Funções e Funcionalidades**.
2. Na página **Antes de começar**, clique em **Seguinte**.
3. Na página do tipo de **instalação Select,** selecione **instalação baseada em funções ou baseada em funções** e, em seguida, clique em **Next**.
4. Na página do servidor de **destino Select,** selecione o servidor atual a partir da piscina do servidor e, em seguida, clique **em Next**.
5. Na página de funções do **servidor Select,** selecione **Acesso Remoto**. Clique em **OK**.
6. Na página **'Seleccionar'** **Next**
7. Na página **de Acesso Remoto,** clique em **Seguinte**.
8. Na página **Role Services,** selecione **Routing**.
9. Aparecerão os pop-ups **Add Roles and Features Wizard.**  Selecione Incluir ferramentas de **gestão (se aplicável)**.  Clique em **adicionar funcionalidades**.
10. Clique em **Seguinte**.
11. Na página **Função de Servidor Web (IIS)**, clique em **Seguinte**.
12. Na página **Selecionar serviços de funções**, clique em **Seguinte**.
13. Na página **Confirmar seleções de instalação**, clique em **Instalar**.
14. Aguarde a página de progresso da **instalação** para indicar que a função de Acesso Remoto está completa.  
15. Clique em **Fechar**.

## <a name="create-virtual-nat-network"></a>Criar rede nat virtual

Agora que todas as funções necessárias foram instaladas, é hora de criar a rede NAT.  O processo de criação envolverá a criação de uma switch e da rede NAT, em si.  Uma rede NAT (tradução de endereços de rede) atribui um endereço IP público a um grupo de VMs numa rede privada para permitir a conectividade à internet.  No nosso caso, o grupo de VMs privados serão os VMs aninhados.  A rede NAT permitirá que os VMs aninhados se comuniquem entre si. Um interruptor é um dispositivo de rede que lida com a receção e encaminhamento de tráfego numa rede.

### <a name="create-a-new-virtual-switch"></a>Criar um novo interruptor virtual

1. Abra **o Gestor de Hiper-V** a partir de Ferramentas Administrativas windows.
2. Selecione o servidor atual no menu de navegação à esquerda.
3. Clique em **Virtual Switch Manager...** do menu **Ações** no lado direito do **Gestor Hyper-V**.
4. No pop-up **do Virtual Switch Manager,** selecione **Internal** para o tipo de interruptor criar.  Clique **em criar interruptor virtual**.
5. Para o recém-criado interruptor virtual, detete o nome para algo memorável.  Para este exemplo, usaremos o 'LabServicesSwitch'.  Clique em **OK**.
6. Será criado um novo adaptador de rede.  O nome será semelhante ao 'vEthernet (LabServicesSwitch)'.  Para verificar abrir o Painel de **Controlo,** clique em **Rede e Internet,** clique em **ver o estado da rede e as tarefas**.  À esquerda, clique em **alterar as definições**do adaptador .

### <a name="create-a-nat-network"></a>Criar uma rede NAT

1. Abra a ferramenta **de encaminhamento e acesso remoto** a partir de ferramentas administrativas do Windows.
2. Selecione o servidor local na página de navegação esquerda.
3. Escolha **configurar de ação** -> **e ativar o encaminhamento e o acesso remoto.**
4. Quando aparecer o Assistente de Configuração do Servidor de **Encaminhamento e Acesso Remoto,** clique em **Next**.
5. Na página **de Configuração,** selecione a configuração de tradução de **endereços da rede (NAT).**  Clique em **Seguinte**.

    >[!WARNING]
    >Não escolha a opção de acesso à rede privada virtual (VPN) e na nat.

6. Na página **na nat Internet Connection,** escolha 'Ethernet'.  Não escolha a ligação 'vEthernet (LabServicesSwitch)' que criámos no Hyper-V Manager. Clique em **Seguinte**.
7. Clique em **Terminar** na última página do assistente.
8. Quando o diálogo **iniciar o serviço** aparecer, clique no Serviço **iniciar**.
9. Espere até que o serviço esteja iniciado.

## <a name="update-network-adapter-settings"></a>Definições de adaptação de rede de atualização

O adaptador de rede será associado ao IP utilizado para o IP de gateway predefinido para a rede NAT criada anteriormente.  Neste exemplo, criamos um endereço IP de 192.168.0.1 com uma máscara de sub-rede de 255.255.255.0.  Usaremos o interruptor virtual criado anteriormente.

1. Abra o Painel de **Controlo,** clique em **Rede e Internet,** clique em **ver o estado da rede e as tarefas**.
2. À esquerda, clique em **alterar as definições**do adaptador .  
3. Na janela De Ligações de Rede, clique duas **vezes** em 'vEthernet (LabServicesSwitch)' para mostrar o diálogo de dados de estado da **vEthernet (LabServicesSwitch).**
4. Clique no botão **Propriedades.**
5. Selecione o item do Protocolo de **Internet 4 (TCP/IPv4)** e clique no botão **Propriedades.**
6. No diálogo de propriedades do Protocolo de **Internet 4 (TCP/IPv4)** selecione **Utilize o seguinte endereço IP**.  Para o endereço IP, insira 192.168.0.1. Para a máscara de sub-rede, introduza 255.255.255.0.  Deixe o gateway em branco.  Deixe os servidores DNS em branco também.

    >[!NOTE]
    > A nossa gama para a nossa rede NAT será, em notação CIDR, 192.168.0.0.0/24.  Isto cria um leque de endereços IP utilizáveis de 192.168.0.1 a 192.168.0.254.  Por convenção, os gateways têm o primeiro endereço IP numa gama de subnet.

7. Clique em OK.

## <a name="create-dhcp-scope"></a>Criar âmbito dHCP

Os seguintes passos são instruções para adicionar o âmbito do DHCP.  Neste artigo, a nossa rede NAT é de 192.168.0.0.0/24 na notação do CIDR.  Isto cria um leque de endereços IP utilizáveis de 192.168.0.1 a 192.168.0.254.  O âmbito de aplicação criado deve estar nessa gama de endereços utilizáveis, excluindo o endereço IP já criado anteriormente.

1. Abra **as Ferramentas Administrativas** e abra a ferramenta administrativa **dHCP.**
2. Na ferramenta **DHCP,** expanda o nó para o servidor atual e selecione **IPv4**.
3. Do menu Ação, escolha **New Scope...**
4. Quando o **Novo Assistente** de Âmbito aparecer, clique em **Next** na página **Welcome.**
5. Na página **Scope Name,** introduza 'LabServicesDhcpScope' ou outra coisa memorável para o nome.  Clique em **Seguinte**.
6. Na página **IP Address Range,** introduza os seguintes valores.

    - 192.168.0.100 para o endereço IP inicial
    - 192.168.0.200 para o endereço IP final
    - 24 para o comprimento
    - 255.255.255.0 para a máscara subnet

7. Clique em **Seguinte**.
8. Na página **Adicionar Exclusões e Atrasar,** clique em **Seguinte**.
9. Na página **de Duração** do Arrendamento, clique em **Seguinte**.
10. Na página **Configure DHCP Options, selecione** **Sim, quero configurar estas opções agora**. Clique em **Seguinte**.
11. No **Router (Gateway Predefinido)**
12. Adicione 192.168.0.1, se ainda não for feito. Clique em **Seguinte**.
13. Na página 'Nome de **Domínio'' e servidores DNS,** adicione 168.63.129.16 como um endereço IP do servidor DNS, se ainda não for feito.  168.63.129.16 é o endereço IP para um servidor DNS estático Azure. Clique em **Seguinte**.
14. Na página **WINS Servers,** clique em **Seguinte**.
15. Uma página de **Activate Scope,** selecione **Sim, quero ativar este âmbito agora**.  Clique em **Seguinte**.
16. Na página De completar a nova página do Assistente de **Âmbito,** clique em **Terminar**.

## <a name="conclusion"></a>Conclusão

Agora a sua máquina de modelo está pronta para criar máquinas virtuais Hyper-V.   Consulte [Criar uma máquina virtual em Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obter instruções sobre como criar máquinas virtuais Hyper-V.  Consulte também o [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) para verificar os sistemas operativos e software disponíveis.

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns à criação de qualquer laboratório.

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)