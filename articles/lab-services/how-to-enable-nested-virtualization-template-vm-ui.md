---
title: Ativar a virtualização aninhada num modelo VM em Azure Lab Services (UI) Microsoft Docs
description: Aprenda a criar um VM modelo com vários VMs no interior.  Por outras palavras, ative a virtualização aninhada num modelo VM em Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: ad92862c78260e7385168faf794c013e85f66b82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445734"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Ativar a virtualização aninhada numa máquina virtual de modelo nos Serviços Azure Lab manualmente

A virtualização aninhada permite-lhe criar um ambiente multi-VM dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá a cada utilizador em laboratório uma máquina virtual configurada com vários VMs dentro do mesmo.  Para obter mais informações sobre a virtualização aninhada e os Serviços de Laboratório Azure, consulte [Ativar a virtualização aninhada numa máquina virtual de modelo nos Serviços Azure Lab](how-to-enable-nested-virtualization-template-vm.md).

Este artigo abrange como configurar a virtualização aninhada numa máquina de modelos nos Serviços de Laboratório Azure usando diretamente funções e ferramentas do Windows.  Há algumas coisas necessárias para permitir que uma classe use a virtualização aninhada.  Os passos abaixo descreverão como configurar manualmente um modelo de máquina de serviços de laboratório com Hyper-V.  As etapas destinam-se ao Windows Server 2016 ou ao Windows Server 2019.  

>[!IMPORTANT]
>Selecione **Large (virtualização aninhada)** ou **Medium (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  A virtualização aninhada não funcionará de outra forma.  

## <a name="enable-hyper-v-role"></a>Ativar o papel do Hiper-V

Os seguintes passos descrevem as ações necessárias para ativar o Hyper-V no Servidor do Windows utilizando o Gestor do Servidor.  Assim que a instalação for bem sucedida, o gestor Hyper-V estará disponível para adicionar, modificar e eliminar máquinas virtuais do cliente.

1. No **Gestor do Servidor**, na página do Dashboard, clique em Adicionar **Funções e Funcionalidades**.
2. Na página **Antes de começar**, clique em **Seguinte**.
3. Na página **do tipo de instalação Select,** mantenha a seleção predefinitiva da instalação baseada em funções ou baseada em recursos e, em seguida, clique em **Seguinte**.
4. Na página **do servidor de destino Select,** selecione Selecione um servidor a partir da piscina do servidor.   O servidor atual já será selecionado.  Clique em Seguinte.
5. Na página **de funções** do servidor Select, selecione **Hyper-V**.  
6. Aparecerá o pop-up **do Add Roles and Features Wizard.**  **Selecione Incluir ferramentas de gestão (se aplicável)**.  Clique no botão **Adicionar Recursos.**
7. Na página **Selecionar papeis de servidor**, clique em **Seguinte**.
8. Na **página 'Selecionar' funcionalidades**, clique em **Seguinte**.
9. Na página **Hyper-V**, clique em **Seguinte**.
10. Na página **'Criar Interruptores Virtuais',** aceita as predefinições e clica em **Seguinte**.
11. Na página **de Migração de Máquinas Virtuais,** aceite os predefinidos e clique em **Seguinte**.
12. Na página **'Lojas Predefinidos',** aceite os predefinidos e clique em **Seguinte**.
13. Na página **de seleções de instalação Confirm,** selecione **Reinicie automaticamente o servidor de destino, se necessário**.
14. Quando aparecer o pop-up **do 'Add Roles and Features Wizard',** clique em **Sim**.
15. Clique em **Install** (Instalar).
16. Aguarde que a página de progresso da **instalação** indique que a função Hyper-V está completa.  A máquina pode reiniciar no meio da instalação.
17. Clique em **Close** (Fechar).

## <a name="enable-dhcp-role"></a>Ativar o papel do DHCP

Quaisquer máquinas virtuais de clientes Hiper-V criadas, precisam de um endereço IP na rede NAT.  Criaremos a rede NAT mais tarde.  Uma forma de atribuir endereços IP é configurar o anfitrião, neste caso o modelo de máquina virtual de laboratório, como um servidor DHCP.  Abaixo estão os passos necessários para ativar o papel de DHCP.

1. No **Gestor do Servidor**, na página do **Dashboard,** clique em **Adicionar Funções e Funcionalidades**.
2. Na página **Antes de começar**, clique em **Seguinte**.
3. Na página **de tipo de instalação Select,** selecione **instalação baseada em funções ou baseada em recursos** e, em seguida, clique em **Seguinte**.
4. Na página do **servidor de destino Select,** selecione o servidor atual a partir da piscina do servidor e, em seguida, clique em **Seguinte**.
5. Na página **de funções** do servidor Select, selecione **O Servidor DHCP**.  
6. Aparecerá o pop-up **do Add Roles and Features Wizard.**  **Selecione Incluir ferramentas de gestão (se aplicável)**.  Clique **em Adicionar Funcionalidades**.

    >[!NOTE]
    >Pode ver um erro de validação indicando que não foram encontrados endereços IP estáticos.  Este aviso pode ser ignorado para o nosso cenário.

7. Na página **Selecionar papeis de servidor**, clique em **Seguinte**.
8. Na página **'Selecionar' funcionalidades,** clique em **Seguinte**.
9. Na página do **Servidor DHCP,** clique em **Seguinte**.
10. Na página **Confirmar seleções de instalação**, clique em **Instalar**.
11. Aguarde que a página de progresso da **instalação** indique que a função DHCP está completa.
12. Clique em Fechar.

## <a name="enable-routing-and-remote-access-role"></a>Ativar a função de encaminhamento e acesso remoto

1. No **Gestor do Servidor**, na página do **Dashboard,** clique em **Adicionar Funções e Funcionalidades**.
2. Na página **Antes de começar**, clique em **Seguinte**.
3. Na página **de tipo de instalação Select,** selecione **instalação baseada em funções ou baseada em recursos** e, em seguida, clique em **Seguinte**.
4. Na página do **servidor de destino Select,** selecione o servidor atual a partir da piscina do servidor e, em seguida, clique em **Seguinte**.
5. Na página de funções do **servidor Select,** selecione **Acesso Remoto**. Clique em **OK**.
6. Na página **'Selecionar' funcionalidades,** clique em **Seguinte**.
7. Na página **'Acesso Remoto',** clique em **Seguinte**.
8. Na página **Serviços de Função,** selecione **Encaminhamento**.
9. Aparecerá o pop-up **do Add Roles and Features Wizard.**  **Selecione Incluir ferramentas de gestão (se aplicável)**.  Clique **em Adicionar Funcionalidades**.
10. Clique em **Seguinte**.
11. Na página **Função de Servidor Web (IIS)**, clique em **Seguinte**.
12. Na página **Selecionar serviços de funções**, clique em **Seguinte**.
13. Na página **Confirmar seleções de instalação**, clique em **Instalar**.
14. Aguarde que a página de progresso da **instalação** indique que a função de Acesso Remoto está completa.  
15. Clique em **Close** (Fechar).

## <a name="create-virtual-nat-network"></a>Criar rede NAT virtual

Agora que todas as funções necessárias foram instaladas, é hora de criar a rede NAT.  O processo de criação implicará a criação de um switch e a própria rede NAT.  Uma rede NAT (tradução de endereços de rede) atribui um endereço IP público a um grupo de VMs numa rede privada para permitir a conectividade à internet.  No nosso caso, o grupo de VMs privados serão os VMs aninhados.  A rede NAT permitirá que os VMs aninhados se comuniquem entre si. Um interruptor é um dispositivo de rede que lida com o recebimento e encaminhamento do tráfego numa rede.

### <a name="create-a-new-virtual-switch"></a>Criar um novo interruptor virtual

1. Abra **o Hyper-V Manager** a partir de ferramentas administrativas do Windows.
2. Selecione o servidor atual no menu de navegação à esquerda.
3. Clique **em Virtual Switch Manager...** do menu **Ações** no lado direito do **Hyper-V Manager**.
4. No pop-up **do Gestor da Switch Virtual,** selecione **Internal** para o tipo de comutação a criar.  Clique **em Criar Switch Virtual**.
5. Para o recém-criado switch virtual, desemote o nome em algo memorável.  Para este exemplo, usaremos 'LabServicesSwitch'.  Clique em **OK**.
6. Será criado um novo adaptador de rede.  O nome será semelhante ao 'vEthernet (LabServicesSwitch)'.  Para verificar a abertura do **Painel de Controlo**, clique em Rede e **Internet,** clique em **Ver o estado da rede e as tarefas**.  À esquerda, clique nas **definições do adaptador Change**.

### <a name="create-a-nat-network"></a>Criar uma rede NAT

1. Abra a ferramenta **de encaminhamento e acesso remoto a** partir de ferramentas administrativas do Windows.
2. Selecione o servidor local na página de navegação à esquerda.
3. Escolha **Configurar Ação**  ->  **e Ative o Encaminhamento e o Acesso Remoto**.
4. Quando aparecer **o Assistente de Configuração do Servidor de Encaminhamento e Acesso Remoto,** clique em **Seguinte**.
5. Na página **de Configuração,** selecione a configuração **de tradução de endereços de rede (NAT).**  Clique em **Seguinte**.

    >[!WARNING]
    >Não escolha a opção de acesso "Rede Privada Virtual (VPN) e NAT".

6. Na página **NAT Internet Connection,** escolha 'Ethernet'.  Não escolha a ligação 'vEthernet (LabServicesSwitch)' que criamos no Hyper-V Manager. Clique em **Seguinte**.
7. Clique em **Terminar** na última página do assistente.
8. Quando **o diálogo de serviço iniciar** aparece, clique em Iniciar **Serviço**.
9. Espere até que o serviço seja iniciado.

## <a name="update-network-adapter-settings"></a>Atualizar as definições do adaptador de rede

O adaptador de rede será associado ao IP utilizado para o IP de gateway predefinido para a rede NAT criada anteriormente.  Neste exemplo, criamos um endereço IP de 192.168.0.1 com uma máscara de sub-rede de 255.255.255.0.  Usaremos o interruptor virtual criado anteriormente.

1. Abra o **Painel de Controlo,** clique em **Rede e Internet,** clique **em Ver Ver estado da rede e tarefas**.
2. À esquerda, clique nas **definições do adaptador Change**.  
3. Na janela **'vEthernet** (LabServicesSwitch)' para mostrar o diálogo de detalhes de **estado vEthernet (LabServicesSwitch).**
4. Clique no botão **Propriedades.**
5. Selecione o item **versão 4 (TCP/IPv4)** do Protocolo de Internet e clique no botão **Propriedades.**
6. No protocolo de **Internet Versão 4 (TCP/IPv4) Diálogo de propriedades,** selecione **Utilize o seguinte endereço IP**.  Para o endereço IP, insira 192.168.0.1. Para a máscara de sub-rede, introduza 255.255.255.0.  Deixe o portão padrão em branco.  Deixe os servidores DNS em branco também.

    >[!NOTE]
    > A nossa gama para a nossa rede NAT será, na notação CIDR, 192.168.0.0/24.  Isto cria uma gama de endereços IP utilizáveis de 192.168.0.1 a 192.168.0.254.  Por convenção, os gateways têm o primeiro endereço IP numa gama de sub-redes.

7. Clique em OK.

## <a name="create-dhcp-scope"></a>Criar âmbito DHCP

Os seguintes passos são instruções para adicionar o âmbito DHCP.  Neste artigo, a nossa rede NAT é 192.168.0.0/24 na notação CIDR.  Isto cria uma gama de endereços IP utilizáveis de 192.168.0.1 a 192.168.0.254.  O âmbito criado deve estar nesse leque de endereços utilizáveis, excluindo o endereço IP já criado anteriormente.

1. Abra **as Ferramentas Administrativas** e abra a ferramenta administrativa **DHCP.**
2. Na ferramenta **DHCP,** expanda o nó para o servidor atual e selecione **O IPv4**.
3. A partir do menu Ação, escolha **Novo Âmbito...**
4. Quando o **Novo Assistente de Âmbito** aparecer, clique em **Seguinte** na página **Welcome.**
5. Na página **'Nome do Âmbito'** insira 'LabServicesDhcpScope' ou algo mais memorável para o nome.  Clique em **Seguinte**.
6. Na página **gama de endereços IP,** insira os seguintes valores.

    - 192.168.0.100 para o endereço IP iniciar
    - 192.168.0.200 para o endereço IP final
    - 24 para o Comprimento
    - 255.255.255.0 para a máscara Subnet

7. Clique em **Seguinte**.
8. Na página **Adicionar Exclusões e Atraso,** clique em **Seguinte**.
9. Na página **'Duração do Arrendamento',** clique em **Seguinte**.
10. Na página **Configure DHCP Opções,** selecione **Sim, quero configurar estas opções agora**. Clique em **Seguinte**.
11. No **Router (Portal Padrão)**
12. Adicione 192.168.0.1, se ainda não for feito. Clique em **Seguinte**.
13. Na página **'Nome de Domínio e servidores DNS',** adicione 168.63.129.16 como endereço IP do servidor DNS, se ainda não for feito.  168.63.129.16 é o endereço IP de um servidor DNS estático Azure. Clique em **Seguinte**.
14. Na página **WINS Servers,** clique em **Seguinte**.
15. Uma página do **Ative Scope,** selecione **Sim, quero ativar este âmbito agora**.  Clique em **Seguinte**.
16. Na página **'Completar o Novo Assistente de Âmbito',** clique em **Terminar**.

## <a name="conclusion"></a>Conclusão

Agora a sua máquina de modelo está pronta para criar máquinas virtuais Hyper-V.   Consulte [Criar uma máquina virtual em Hiper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obter instruções sobre como criar máquinas virtuais Hiper-V.  Consulte também o [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) para verificar os sistemas operativos e o software disponíveis.

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)