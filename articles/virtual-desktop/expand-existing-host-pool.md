---
title: Expandir a piscina de anfitriões existente com novos anfitriões de sessão - Azure
description: Como expandir um pool de anfitriões existente com novos anfitriões de sessão no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365224"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Expandir uma piscina de anfitriões existente com novos anfitriões de sessão

À medida que aumenta o uso dentro da sua piscina de anfitriões, poderá ter de expandir a sua piscina de anfitriões existente com novos anfitriões de sessão para lidar com a nova carga.

Este artigo irá dizer-lhe como você pode expandir uma piscina de anfitriões existente com novos anfitriões de sessão.

## <a name="what-you-need-to-expand-the-host-pool"></a>O que você precisa para expandir a piscina anfitriã

Antes de começar, certifique-se de que criou uma piscina de anfitriões e máquinas virtuais de anfitrião de sessão (VMs) utilizando um dos seguintes métodos:

- [Oferta do Azure Marketplace](./create-host-pools-azure-marketplace.md)
- [Modelo de Gestor de Recursos GitHub Azure](./create-host-pools-arm-template.md)
- [Criar um conjunto de anfitriões com o PowerShell](./create-host-pools-powershell.md)

Você também vai precisar das seguintes informações a partir de quando você criou o pool anfitrião e vMs anfitrião de sessão:

- VM tamanho, imagem e prefixo de nome
- Domain juntam-se e Windows Virtual Desktop credenciais de administrador de inquilino
- Nome de rede virtual e nome da sub-rede

As próximas três secções são três métodos que pode usar para expandir a piscina anfitriã. Pode fazer qualquer uma das ferramentas de implantação com que se sinta confortável.

>[!NOTE]
>Durante a fase de implementação, verá mensagens de erro para os recursos VM da sessão anterior se estiverem desligados. Estes erros acontecem porque o Azure não pode executar a extensão DoSC PowerShell para validar que os VMs anfitriãos da sessão estão corretamente registados na sua piscina de anfitriões existente. Pode ignorar com segurança estes erros, ou pode evitar os erros iniciando todos os VMs hospedeiros da sessão na piscina de anfitriões existente antes de iniciar o processo de implementação.

## <a name="redeploy-from-azure"></a>Reimplantação de Azure

Se já criou um anfitrião e vMs de anfitrião de sessão usando a [oferta do Azure Marketplace](./create-host-pools-azure-marketplace.md) ou o modelo [GitHub Azure Resource Manager,](./create-host-pools-arm-template.md)pode reimplantar o mesmo modelo do portal Azure. A recolocação do modelo reintroduz automaticamente todas as informações que introduziu no modelo original, exceto as palavras-passe.

Aqui está como reimplantar o modelo do Gestor de Recursos Azure para expandir um pool de anfitriões:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. A partir da barra de pesquisa no topo do portal Azure, procure **por grupos de Recursos** e selecione o item em **Serviços**.
3. Encontre e selecione o grupo de recursos que criou quando fez a piscina anfitriã.
4. No painel do lado esquerdo do navegador, selecione **Implementações**.
5. Selecione a implementação adequada para o seu processo de criação de piscina anfitrião:
     - Se criou o pool de anfitriões original com a oferta do Azure Marketplace, selecione a implementação a partir de **rds.wvd-provision-host-pool**.
     - Se criou o conjunto de anfitriões original com o modelo GitHub Azure Resource Manager, selecione a implementação chamada **Microsoft.Template**.
6. Selecione **Recolocar**.
     
     >[!NOTE]
     >Se o modelo não recolocar automaticamente quando selecionar **Reimplementar**, selecione **O Modelo** no painel do lado esquerdo do seu navegador e, em seguida, selecione **Deploy**.

7. Selecione o grupo de recursos que contém os VMs anfitriões da sessão atual na piscina de anfitriões existente.
     
     >[!NOTE]
     >Se vir um erro que lhe diga para selecionar um grupo de recursos diferente, mesmo que o que inseriu esteja correto, selecione outro grupo de recursos e, em seguida, selecione o grupo de recursos originais.

8. Introduza o seguinte URL para o *_artifactsLocation:*`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Insira o novo número total de anfitriões de sessão que deseja no *Número rdsh de instâncias*. Por exemplo, se estiver a expandir a sua piscina de anfitriões de cinco anfitriões para oito sessões, insira **8**.
10. Introduza a mesma senha de domínio existente que utilizou para o domínio existente UPN. Não altere o nome de utilizador, pois isso causará um erro ao executar o modelo.
11. Introduza a mesma senha de administração de inquilino que usou para o utilizador ou id de aplicação que inseriu para *O Inquilino Administrador Upn ou Id*de Aplicação . Mais uma vez, não mude o nome de utilizador.
12. Complete a submissão para expandir a sua piscina anfitriã.

## <a name="run-the-azure-marketplace-offering"></a>Executar a oferta do Azure Marketplace

Siga as instruções em [Create a host pool utilizando o Azure Marketplace](./create-host-pools-azure-marketplace.md) até chegar à Run the [Azure Marketplace oferecendo-se para fornecer uma nova piscina de anfitriões.](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool) Quando chegar a esse ponto, terá de introduzir as seguintes informações para cada separador:

### <a name="basics"></a>Noções básicas

Todos os valores desta secção devem corresponder ao que forneceu quando criou pela primeira vez os VMs de hospedada e anfitrião da sessão, com exceção dos utilizadores de ambientes de *trabalho Predefinidos:*

1.    Para *Subscrição,* selecione a subscrição onde criou pela primeira vez o pool anfitrião.
2.    Para o *grupo Resource,* selecione o mesmo grupo de recursos onde estão localizados os VMs hospedeiros de anfitrião da sessão de hospedas existentes.
3.    Para *a Região,* selecione a mesma região onde estão localizadas as VMs anfitriãs da sessão de acolhimento existentes.
4.    Para *o nome Hostpool,* insira o nome da piscina hospedeira existente.
5.    Para *o tipo desktop,* selecione o tipo de ambiente de trabalho que corresponde à piscina de anfitriões existente.
6.    Para *utilizadores de ambientede trabalho Predefinidos,* introduza uma lista separada da vírvia de quaisquer utilizadores adicionais que pretenda inscrever-se nos clientes do Windows Virtual Desktop e aceda a um ambiente de trabalho após o Azure Marketplace oferecer acabamentos. Por exemplo, se quiser user3@contoso.com atribuir user4@contoso.com e user3@contoso.comaceder, insira.user4@contoso.com
7.    Selecione **Seguinte: Configurar**a máquina virtual .

>[!NOTE]
>Com exceção dos utilizadores de ambientes de *trabalho Predefinidos,* todos os campos devem corresponder exatamente ao que foi configurado na piscina de anfitriões existente. Se houver um desfasamento que resultará numa nova piscina de anfitriões.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Todos os valores dos parâmetros nesta secção devem corresponder ao que forneceu quando criou pela primeira vez os VMs anfitriãos da piscina e da sessão, com exceção do número total de VMs. O número de VMs que introduzir será o número de VMs na sua piscina de anfitriões expandida:

1. Selecione o tamanho VM que corresponde às VMs anfitriãs da sessão existente.
    
    >[!NOTE]
    >Se o tamanho de VM específico que procura não aparecer no seletor de tamanho VM, isso é porque ainda não o embarcamos na ferramenta Azure Marketplace. Para solicitar um tamanho VM, crie um pedido ou revote um pedido existente no [fórum Windows Virtual Desktop UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Personalize o Perfil de *Utilização*, *Utilizadores Totais*e *Número de parâmetros de máquinas virtuais* para selecionar o número total de anfitriões de sessão que gostaria de ter na sua piscina anfitriã. Por exemplo, se estiver a expandir o seu pool de anfitriões de cinco anfitriões para oito sessões, configure estas opções para chegar a 8 máquinas virtuais.
3. Introduza um prefixo para os nomes das máquinas virtuais. Por exemplo, se introduzir o nome "prefixo", as máquinas virtuais serão chamadas de "prefixo-0", "prefixo-1", e assim por diante.
4. Selecione **Seguinte : Definições de máquinavirtual**.

### <a name="virtual-machine-settings"></a>Definições da máquina virtual

Todos os valores dos parâmetros nesta secção devem corresponder ao que forneceu quando criou pela primeira vez os VMs anfitriãos e anfitriões da sessão:

1. Para *a imagem de origem* e versão Image *OS,* introduza as mesmas informações que forneceu quando criou o pool anfitrião pela primeira vez.
2. Para *que o domínio AD se junte à UPN* e às palavras-passe associadas, introduza as mesmas informações que forneceu quando criou o pool anfitrião para se juntar aos VMs ao domínio do Diretório Ativo. Estas credenciais serão usadas para criar uma conta local nas suas máquinas virtuais. Pode redefinir estas contas locais para alterar as suas credenciais mais tarde.
3. Para obter informações de rede virtual, selecione a mesma rede virtual e subnet para onde estão localizados os VMs de anfitrião da sessão de hospedaria existentes.
4. Selecione **Seguinte : Configure as informações do Windows Virtual Desktop**.

### <a name="windows-virtual-desktop-information"></a>Informações sobre desktop virtual do Windows

Todos os valores dos parâmetros nesta secção devem corresponder ao que forneceu quando criou pela primeira vez os VMs anfitriãos e anfitriões da sessão:

1. Para o nome do grupo de *inquilinos Windows Virtual Desktop,* insira o nome do grupo de inquilinos que contém o seu inquilino. Deixe-o como o padrão, a menos que lhe tenha sido dado um nome específico de grupo de inquilinos.
2. Para o nome do *inquilino Do Windows Virtual Desktop,* insira o nome do inquilino onde estará a criar esta piscina de hospedada.
3. Especifique as mesmas credenciais que usou quando criou pela primeira vez os VMs anfitriãos da piscina e da sessão. Se estiver a utilizar um diretor de serviço, insira a identificação da instância do Diretório Ativo Azure onde está localizado o seu diretor de serviço.
4. Selecione **Seguinte : Rever + criar**.

## <a name="run-the-github-azure-resource-manager-template"></a>Executar o modelo gitHub Azure Resource Manager

Siga as instruções no [modelo Run the Azure Resource Manager para fornecer uma nova piscina de anfitriões](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) e fornecer todos os mesmos valores de parâmetro, exceto para o *Número Rdsh de Instâncias*. Insira o número de VMs anfitrião da sessão que deseja na piscina anfitriã depois de executar o modelo. Por exemplo, se estiver a expandir a sua piscina de anfitriões de cinco anfitriões para oito sessões, insira **8**.

## <a name="next-steps"></a>Passos seguintes

Agora que expandiu o seu pool de anfitriões existente, pode iniciar sessão com um cliente do Windows Virtual Desktop para os testar como parte de uma sessão de utilizador. Pode ligar-se a uma sessão com qualquer um dos seguintes clientes:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](./connect-windows-7-and-10.md)
- [Ligar com o cliente web](./connect-web.md)
- [Ligar ao cliente Android](./connect-android.md)
- [Ligar ao cliente de macOS](./connect-macos.md)
- [Ligar ao cliente de iOS](./connect-ios.md)
