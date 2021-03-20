---
title: Expandir o pool de anfitriões virtual do Windows Virtual (clássico) com novos anfitriões de sessão - Azure
description: Como expandir um pool de anfitriões existente com novos anfitriões de sessão no Windows Virtual Desktop (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61cf28b0f1ebee6a0312ec3f23f22b01c6c4919e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88009176"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Expandir uma piscina de anfitriões existente com novos anfitriões de sessão no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../expand-existing-host-pool.md).

À medida que aumenta a utilização dentro da sua piscina de anfitriões, poderá ter de expandir a sua piscina de anfitriões existente com novos anfitriões de sessão para lidar com a nova carga.

Este artigo dir-lhe-á como pode expandir uma piscina de anfitriões existente com novos anfitriões de sessão.

## <a name="what-you-need-to-expand-the-host-pool"></a>O que você precisa para expandir a piscina de anfitriões

Antes de começar, certifique-se de que criou uma piscina de anfitriões e máquinas virtuais de anfitrião (VMs) utilizando um dos seguintes métodos:

- [Oferta do Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Modelo de gestor de recursos do GitHub Azure](create-host-pools-arm-template.md)
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell-2019.md)

Você também precisará das seguintes informações a partir de quando criou o anfitrião pool e vMs anfitrião de sessão:

- Tamanho vM, imagem e prefixo de nome
- Adere ao domínio e as credenciais de administrador de inquilinos do Windows Virtual Desktop
- Nome de rede virtual e nome da sub-rede

As próximas três secções são três métodos que pode usar para expandir a piscina hospedeira. Pode fazer qualquer uma das ferramentas de implantação com que se sinta confortável.

>[!NOTE]
>Durante a fase de implantação, verá mensagens de erro para os recursos VM anfitrião da sessão anterior se forem atualmente desligadas. Estes erros acontecem porque o Azure não pode executar a extensão DSC powerShell para validar que os VMs do anfitrião da sessão estão corretamente registados no seu pool de anfitriões existente. Pode ignorar com segurança estes erros, ou pode evitar os erros iniciando todos os VMs do anfitrião da sessão no pool anfitrião existente antes de iniciar o processo de implementação.

## <a name="redeploy-from-azure"></a>Reimplantação de Azure

Se já criou um VMs anfitrião de pool e sessão de anfitriões utilizando a oferta do [Azure Marketplace](create-host-pools-azure-marketplace-2019.md) ou [o modelo GitHub Azure Resource Manager,](create-host-pools-arm-template.md)pode recolocar o mesmo modelo a partir do portal Azure. Recolocar o modelo reencamenta automaticamente todas as informações que introduziu no modelo original, com exceção das palavras-passe.

Aqui está como recolocar o modelo do Gestor de Recursos Azure para expandir uma piscina hospedeira:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. A partir da barra de pesquisa no topo do portal Azure, procure **grupos de Recursos** e selecione o item em **Serviços**.
3. Encontre e selecione o grupo de recursos que criou quando fez a piscina de anfitrião.
4. No painel do lado esquerdo do navegador, selecione **Implementações**.
5. Selecione a implementação adequada para o processo de criação de piscina de anfitrião:
     - Se criou a piscina de anfitriões original com a oferta do Azure Marketplace, selecione a implementação a partir **de rds.wvd-provision-host-pool**.
     - Se criou o pool de anfitriões original com o modelo GitHub Azure Resource Manager, selecione a implementação chamada **Microsoft.Template**.
6. Selecione **Recolocação**.

     >[!NOTE]
     >Se o modelo não for reempreso automaticamente quando selecionar **Recolocação**, selecione **o Modelo** no painel no lado esquerdo do seu navegador e, em seguida, selecione **Implementar**.

7. Selecione o grupo de recursos que contém os VMs de anfitrião de sessão atuais na piscina hospedeira existente.

     >[!NOTE]
     >Se vir um erro que lhe diga para selecionar um grupo de recursos diferente, mesmo que o que introduziu esteja correto, selecione outro grupo de recursos e, em seguida, selecione o grupo de recursos original.

8. Introduza o seguinte URL para o *_artifactsLocation:*`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Insira o novo número total de anfitriões de sessão que pretende para *o Número de Ocorrências de Rdsh*. Por exemplo, se estiver a expandir o seu pool de anfitriões de cinco sessão para oito, insira **8**.
10. Introduza a mesma senha de domínio existente que usou para o domínio existente UPN. Não altere o nome de utilizador, porque isso causará um erro quando executar o modelo.
11. Introduza a mesma senha de administração de inquilino que usou para o ID do utilizador ou aplicação que inseriu para *o Tenant Admin Upn ou Application Id*. Mais uma vez, não mude o nome de utilizador.
12. Complete a submissão para expandir a sua piscina de anfitriões.

## <a name="run-the-azure-marketplace-offering"></a>Executar a oferta do Azure Marketplace

Siga as instruções em [Criar uma piscina de anfitriões utilizando o Azure Marketplace](create-host-pools-azure-marketplace-2019.md) até chegar ao Run the [Azure Marketplace oferecendo uma nova piscina de anfitriões.](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool) Quando chegar a esse ponto, terá de introduzir as seguintes informações para cada separador:

### <a name="basics"></a>Noções básicas

Todos os valores nesta secção devem corresponder ao que forneceu quando criou os VMs de anfitrião e de anfitrião de sessão, com exceção *dos utilizadores de ambientes de trabalho predefinidos:*

1.    Para *Subscrição*, selecione a subscrição onde criou pela primeira vez o pool de anfitriões.
2.    Para o *grupo de recursos*, selecione o mesmo grupo de recursos onde estão localizados os VMs de anfitrião da sessão de anfitriões existentes.
3.    Para *a Região*, selecione a mesma região onde estão localizados os VMs anfitriões de piscina existentes.
4.    Para *o nome Hostpool,* insira o nome da piscina de anfitriões existente.
5.    Para *o tipo desktop*, selecione o tipo de ambiente de trabalho que corresponde à piscina hospedeira existente.
6.    Para *os utilizadores de desktop predefinidos*, introduza uma lista separada de vírgula de quaisquer utilizadores adicionais que pretenda iniciar súmula nos clientes do Windows Virtual Desktop e aceda a um ambiente de trabalho após o acabamento da oferta do Azure Marketplace. Por exemplo, se quiser atribuir user3@contoso.com e user4@contoso.com aceder, insira user3@contoso.com , user4@contoso.com .
7.    Selecione **Seguinte : Configurar máquina virtual**.

>[!NOTE]
>Com exceção *dos utilizadores de ambientes de trabalho predefinidos,* todos os campos devem corresponder exatamente ao que foi configurado na piscina hospedeira existente. Se houver uma incompatibilidade que resultará numa nova piscina de anfitriões.

### <a name="configure-virtual-machines"></a>Configure máquinas virtuais

Todos os valores de parâmetros nesta secção devem corresponder ao que forneceu quando criou os VMs de anfitrião e de anfitrião de sessão, com exceção do número total de VMs. O número de VMs que você inseriu será o número de VMs na sua piscina de anfitriões expandida:

1. Selecione o tamanho VM que corresponda aos VMs do anfitrião da sessão existente.

    >[!NOTE]
    >Se o tamanho específico do VM que procura não aparece no seletor de tamanho vM, isso é porque ainda não o acedemos à ferramenta Azure Marketplace. Para solicitar um tamanho VM, crie um pedido ou antevou um pedido existente no [fórum Virtual Desktop UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)do Windows .

2. Personalize o *Perfil de Utilização,* total de *utilizadores* e *número de parâmetros de máquinas virtuais* para selecionar o número total de anfitriões de sessão que gostaria de ter na piscina de anfitriões. Por exemplo, se estiver a expandir o seu pool de anfitriões de cinco sessão para oito, configuure estas opções para chegar a 8 máquinas virtuais.
3. Introduza um prefixo para os nomes das máquinas virtuais. Por exemplo, se introduzir o nome "prefixo", as máquinas virtuais serão chamadas de "prefix-0", "prefixo-1", e assim por diante.
4. Selecione **Seguinte : Configurações de máquinas virtuais**.

### <a name="virtual-machine-settings"></a>Definições da máquina virtual

Todos os valores de parâmetros nesta secção devem corresponder ao que forneceu quando criou os VMs do anfitrião e do anfitrião da sessão:

1. Para *a origem de imagem* e versão Image *OS,* insira as mesmas informações que forneceu quando criou a piscina de anfitrião pela primeira vez.
2. Para *o domínio AD juntar-se à UPN* e às palavras-passe associadas, insira as mesmas informações que forneceu quando criou o pool anfitrião para se juntar aos VMs ao domínio do Ative Directory. Estas credenciais serão usadas para criar uma conta local nas suas máquinas virtuais. Pode redefinir estas contas locais para alterar as suas credenciais mais tarde.
3. Para obter informações sobre a rede virtual, selecione a mesma rede virtual e sub-rede para onde estão localizados os VMs anfitriões da sessão de anfitriões existentes.
4. Selecione **Seguinte : Configurar informações de ambiente de trabalho virtual do Windows**.

### <a name="windows-virtual-desktop-information"></a>Informações de ambiente de trabalho virtual do Windows

Todos os valores de parâmetros nesta secção devem corresponder ao que forneceu quando criou os VMs do anfitrião e do anfitrião da sessão:

1. Para *o nome do grupo de inquilinos virtual do Windows Desktop,* insira o nome para o grupo de inquilinos que contém o seu inquilino. Deixe-o como padrão, a menos que lhe tenha sido fornecido um nome específico do grupo de inquilinos.
2. Para *o nome do inquilino virtual do Windows Desktop,* insira o nome do inquilino onde estará a criar esta piscina de anfitriões.
3. Especifique as mesmas credenciais que usou quando criou os VMs do anfitrião e do anfitrião da sessão. Se estiver a utilizar um diretor de serviço, insira o ID do Azure Ative Directory onde está localizado o seu principal de serviço.
4. Selecione **Seguinte : Rever + criar**.

## <a name="run-the-github-azure-resource-manager-template"></a>Executar o modelo gitHub Azure Resource Manager

Siga as instruções em [Executar o modelo Azure Resource Manager para o fornecimento de uma nova piscina de anfitriões](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) e fornecer todos os mesmos valores de parâmetro, com exceção do *Número de Ocorrências do Rdsh*. Introduza o número de VMs do anfitrião de sessão que deseja na piscina de anfitrião depois de executar o modelo. Por exemplo, se estiver a expandir o seu pool de anfitriões de cinco sessão para oito, insira **8**.

## <a name="next-steps"></a>Passos seguintes

Agora que expandiu o seu pool de anfitriões existente, pode iniciar sessão num cliente do Windows Virtual Desktop para testá-los como parte de uma sessão de utilizador. Pode ligar-se a uma sessão com qualquer um dos seguintes clientes:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-10-2019.md)
- [Ligar com o cliente web](connect-web-2019.md)
- [Ligar ao cliente Android](connect-android-2019.md)
- [Ligar ao cliente de macOS](connect-macos-2019.md)
- [Ligar ao cliente de iOS](connect-ios-2019.md)
