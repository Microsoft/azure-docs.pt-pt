---
title: Configurar o plano de recuperação de desastres virtual do Windows Desktop - Azure
description: Como configurar um plano de continuidade de negócios e recuperação de desastres para a sua implementação virtual do Windows Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 968f82a143872fd282222539ab71a70db488a20d
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935768"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>Criar um plano de continuidade de negócios e recuperação de desastres

Para manter os dados da sua organização seguros, poderá ter de adotar uma estratégia de continuidade de negócios e recuperação de desastres (BCDR). Uma estratégia sonora do BCDR mantém as suas apps e carga de trabalho em funcionamento durante o serviço planeado e não planeado ou paragens do Azure.

O Windows Virtual Desktop oferece BCDR para o serviço de ambiente de trabalho virtual do Windows para preservar os metadados do cliente durante as interrupções. Quando ocorre uma paralisação numa região, os componentes da infraestrutura de serviço falharão até à localização secundária e continuarão a funcionar normalmente. Ainda é possível aceder aos metadados relacionados com o serviço e os utilizadores ainda podem ligar-se aos anfitriões disponíveis. As ligações do utilizador final permanecerão on-line enquanto o ambiente de inquilino ou os anfitriões permanecerem acessíveis.

Para se certificar de que os utilizadores ainda podem ligar-se durante uma paragem da região, é necessário replicar as suas máquinas virtuais (VMs) num local diferente. Durante as interrupções, o local primário falha nos VMs replicados no local secundário. Os utilizadores podem continuar a aceder a apps a partir da localização secundária sem interrupção. Além da replicação de VM, terá de manter as identidades dos utilizadores acessíveis no local secundário. Se estiver a usar recipientes de perfil, também terá de os replicar. Por fim, certifique-se de que as aplicações empresariais que dependem de dados na localização primária podem falhar com os restantes dados.

Para resumir, para manter os seus utilizadores ligados durante uma paragem, terá de fazer as seguintes coisas nesta ordem:

- Replique os VMs num local secundário.
- Se estiver a utilizar recipientes de perfil, ercaneça a replicação de dados no local secundário.
- Certifique-se de que as identidades do utilizador configuradas na localização primária estão disponíveis no local secundário.
- Certifique-se de que quaisquer aplicações de linha de negócios que dependam de dados na sua localização primária sejam falhadas na localização secundária.

## <a name="vm-replication"></a>Replicação de VMs

Primeiro, terás de replicar os teus VMs para o local secundário. As suas opções para o fazer dependem da configuração dos seus VMs:

- Você pode configurar todos os seus VMs para piscinas de anfitriões em conjunto e pessoais com Azure Site Recovery. Com este método, você só precisará configurar um pool de anfitriões e seus grupos de aplicações relacionados e espaços de trabalho.
- Você pode criar uma nova piscina de anfitriões na região de failover, mantendo todos os recursos na sua localização de failover desligado. Para este método, você precisaria de configurar novos grupos de aplicações e espaços de trabalho na região de failover. Em seguida, pode utilizar um plano de recuperação do local de Azure para ligar as piscinas hospedeiras.
- Você pode criar uma piscina hospedeira que é povoada por VMs construídos nas regiões primárias e de failover, mantendo os VMs na região de failover desligados. Neste caso, você só precisa configurar um pool de anfitriões e seus grupos de aplicações relacionados e espaços de trabalho. Você pode usar um plano de recuperação do local Azure para alimentar em piscinas hospedeiras com este método.

Recomendamos que utilize [a Recuperação do Sítio azul](../site-recovery/site-recovery-overview.md) para gerir a replicação de VMs em outros locais Azure, conforme descrito na arquitetura de [recuperação de desastres Azure-to-Azure](../site-recovery/azure-to-azure-architecture.md). Recomendamos especialmente a utilização da Recuperação do Site Azure para piscinas de anfitriões pessoais, porque a Recuperação do Site Azure suporta [skus baseados em servidores e clientes.](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems)

Se utilizar a Recuperação do Sítio Azure, não necessitará de registar estes VMs manualmente. O agente virtual de ambiente de trabalho do Windows no VM secundário utilizará automaticamente o mais recente token de segurança para ligar à instância de serviço mais próxima do mesmo. O VM (anfitrião da sessão) no local secundário passará automaticamente a fazer parte da piscina hospedeira. O utilizador final terá de voltar a ligar-se durante o processo, mas para além disso, não existem outras operações manuais.

Se existirem ligações de utilizador existentes durante a paralisação, antes que o administrador possa começar a falhar na região secundária, é necessário acabar com as ligações do utilizador na região atual.

Para desligar os utilizadores no Windows Virtual Desktop (clássico), execute este cmdlet:

```powershell
Invoke-RdsUserSessionLogoff
```

Para desligar os utilizadores na versão integrada do Windows Virtual Desktop, execute este cmdlet:

```powershell
Remove-AzWvdUserSession
```

Depois de ter assinado todos os utilizadores na região primária, pode falhar sobre os VMs na região primária e deixar que os utilizadores se conectem aos VMs na região secundária. Para obter mais informações sobre como este processo funciona, consulte [Os VMs Replicate Azure para outra região do Azure.](../site-recovery/azure-to-azure-how-to-enable-replication.md)

## <a name="virtual-network"></a>Rede virtual

Em seguida, considere a conectividade da sua rede durante a paragem. Terá de se certificar de que criou uma rede virtual (VNET) na sua região secundária. Se os seus utilizadores precisarem de aceder aos recursos no local, terá de configurar este VNET para aceder aos mesmos. Pode estabelecer ligações no local com uma VPN, ExpressRoute ou WAN virtual.

Recomendamos que utilize a Recuperação do Site Azure para configurar o VNET na região de failover, porque preserva as definições da sua rede primária e não precisa de ser espreitada.

## <a name="user-identities"></a>Identidades dos utilizadores

Em seguida, certifique-se de que o controlador de domínio está disponível no local secundário. 

Existem três formas de manter o controlador de domínio disponível:

   - Ter Controlador de Domínio de Diretório Ativo no local secundário
   - Utilize um controlador de domínio de diretório ativo no local
   - Replicar controlador de domínio do diretório ativo usando [a recuperação do site Azure](../site-recovery/site-recovery-active-directory.md)

## <a name="user-and-app-data"></a>Dados de utilizador e aplicativos

Se estiver a usar recipientes de perfil, o próximo passo é configurar a replicação de dados no local secundário. Tem cinco opções para armazenar perfis FSLogix:

   - Espaços de armazenamento Direto (S2D)
   - Unidades de rede (VM com unidades extra)
   - Ficheiros do Azure
   - Azure NetApp Files
   - Cache de nuvem para replicação

Para obter mais informações, consulte [as opções de armazenamento de recipientes de perfil FSLogix no Windows Virtual Desktop](store-fslogix-profile.md).

Se está a preparar a recuperação de desastres para perfis, estas são as suas opções:

   - Configurar a replicação native Azure (por exemplo, replicação de conta de armazenamento standard Azure Files, replicação de ficheiros Azure NetApp ou Sincronização de ficheiros Azure para servidores de ficheiros).
    
     >[!NOTE]
     >A replicação do NetApp é automática depois de a ter configurado pela primeira vez. Com os planos de Recuperação do Site Azure, pode adicionar pré-scripts e pós-scripts para falhar sobre os recursos não-VM replicar recursos de Armazenamento Azure.

   - Configurar o Cache de Nuvem FSLogix para os dados da aplicação e do utilizador.
   - Crie a recuperação de desastres para os dados das aplicações apenas para garantir o acesso a dados críticos do negócio em todos os momentos. Com este método, pode recuperar os dados do utilizador após o fim da paralisação.

Vamos ver como configurar a FSLogix para preparar a recuperação de desastres para cada opção.

### <a name="fslogix-configuration"></a>Configuração FSLogix

O agente FSLogix pode suportar várias localizações de perfil se configurar as entradas de registo para FSLogix.

Para configurar as entradas de registo:

1. Abra o **Editor de Registos.**
2. Vá a **Perfis**  >  **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **FSLogix**de software de HKEY_LOCAL_MACHINE  >  **de**computador.
   
     > [!div class="mx-imgBorder"]
     > ![Uma imagem da janela dos perfis no Editor de Registos. A VHDLocation está selecionada.](media/regedit-profiles.png)

3. Clique com o botão direito em **VHDLocations** e **selecione Editar Multi-String**.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem da janela Editar Multi-String. Os dados de valor listam as localizações centrívais dos EUA e do Leste dos EUA.](media/multi-string-edit.png)

4. No campo **Dados de Valor,** insira os locais que pretende utilizar.
5. Quando tiver terminado, selecione **OK**.

Se a primeira localização não estiver disponível, o agente FSLogix falhará automaticamente para a segunda, e assim por diante.

Recomendamos que configuure o agente FSLogix com um caminho para a localização secundária na região principal. Uma vez que a localização primária seja desligada, o agente FLogix irá replicar-se como parte da replicação da recuperação do local de Azure VM. Uma vez que os VMs replicados estejam prontos, o agente tentará automaticamente o caminho para a região secundária.

Por exemplo, digamos que os VMs anfitriões da sessão primária estão na região central dos EUA, mas o seu contentor de perfil está na região central dos EUA por razões de desempenho.

Neste caso, configuraria o agente FSLogix com um caminho para o armazenamento no centro dos EUA. Configuraria os VMs dos anfitriões da sessão para replicar nos EUA Ocidentais. Assim que o caminho para os EUA central falhar, o agente tentará criar um novo caminho para o armazenamento nos EUA ocidentais.

### <a name="s2d"></a>S2D

Uma vez que o S2D lida com a replicação em regiões internamente, não é necessário configurar manualmente o caminho secundário.

### <a name="network-drives-vm-with-extra-drives"></a>Unidades de rede (VM com unidades extra)

Se replicar os VMs de armazenamento de rede utilizando a recuperação do site Azure como os VMs do anfitrião da sessão, então a recuperação mantém o mesmo caminho, o que significa que não precisa de reconfigurar o FSlogix.

### <a name="azure-files"></a>Ficheiros do Azure

O Azure Files suporta a replicação assíncrona transversal que pode especificar quando cria a conta de armazenamento. Se a natureza assíncrona dos Ficheiros Azure já cobre os seus objetivos de recuperação de desastres, então não precisa de fazer uma configuração adicional.

Se precisar de uma replicação sincronizada para minimizar a perda de dados, recomendamos que utilize a Cache de Nuvem FSLogix.

>[!NOTE]
>Esta secção não cobre o mecanismo de autenticação por falhas para ficheiros Azure.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Saiba mais sobre os ficheiros Azure NetApp na [Create replication peering para Azure NetApp Files](../azure-netapp-files/cross-region-replication-create-peering.md).

## <a name="app-dependencies"></a>Dependências de aplicativos

Por fim, certifique-se de que quaisquer aplicações empresariais que dependam de dados localizados na região primária podem falhar até à localização secundária. Além disso, certifique-se de configurar as configurações que as aplicações precisam para trabalhar no novo local. Por exemplo, se uma das aplicações estiver dependente do backend SQL, certifique-se de replicar SQL na localização secundária. Deve configurar a aplicação para utilizar a localização secundária como parte do processo de failover ou como configuração padrão. Pode modelar dependências de aplicativos em planos de recuperação do local de Azure. Para saber mais, consulte [sobre os planos de recuperação.](../site-recovery/recovery-plan-overview.md)

## <a name="disaster-recovery-testing"></a>Testes de recuperação de desastres

Depois de acabares de preparar a recuperação de desastres, vais querer testar o teu plano para ter a certeza que funciona.

Aqui ficam algumas sugestões para testar o seu plano:

- Se os VMs de teste tiverem acesso à Internet, assumirão qualquer anfitrião de sessão existente para novas ligações, mas todas as ligações existentes ao anfitrião original da sessão permanecerão ativas. Certifique-se de que o administrador que executa os sinais de teste indica todos os utilizadores ativos antes de testar o plano. 
- Só deve fazer testes completos de recuperação de desastres durante uma janela de manutenção para não perturbar os seus utilizadores. Também pode utilizar uma piscina de anfitriões no ambiente de validação para o teste. 
- Certifique-se de que o seu teste abrange todas as aplicações críticas ao negócio.
- Recomendamos que só falhe até 100 VMs de cada vez. Se tiver mais VMs do que isso, recomendamos que os falhe em lotes com 10 minutos de intervalo.

## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas sobre como manter os seus dados seguros, além de planear interrupções, consulte o nosso [guia de segurança.](security-guide.md)