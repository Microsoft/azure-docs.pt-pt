---
title: Configurar o proxy Do Centro de Distribuição de Chaves Kerberos Windows Virtual Desktop - Azure
description: Como configurar uma piscina de anfitriões virtual do Windows desktop para usar um proxy do Centro de Distribuição de Chaves Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: bcf28fbc0d2f4ec9eeac5bcb8f0b2c9b65a62b6b
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775046"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Configure um proxy do Centro de Distribuição de Chaves Kerberos (pré-visualização)

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Clientes conscientes da segurança, como organizações financeiras ou governamentais, muitas vezes assinam usando smartcards. Os smartcards tornam as implementações mais seguras exigindo a autenticação multifactor (MFA). No entanto, para a parte RDP de uma sessão de Desktop Virtual do Windows, os Smartcards requerem uma ligação direta, ou "linha de visão", com um controlador de domínio Ative Directory (AD) para a autenticação kerberos. Sem esta ligação direta, os utilizadores não podem iniciar sussionalmente na rede da organização a partir de ligações remotas. Os utilizadores de uma implementação virtual do Windows desktop podem utilizar o serviço de procuração KDC para proxy este tráfego de autenticação e assinar remotamente. O proxy KDC permite a autenticação para o Protocolo de Ambiente de Trabalho Remoto de uma sessão de Desktop Virtual do Windows, permitindo que o utilizador assine com segurança. Isto torna o trabalho a partir de casa muito mais fácil, e permite que certos cenários de recuperação de desastres funcionem de forma mais suave.

No entanto, a configuração do proxy KDC normalmente envolve a atribuição da função de Gateway do Servidor do Windows no Windows Server 2016 ou mais tarde. Como utilizar uma função de Serviços de Secretária remoto para iniciar sismo no Windows Virtual Desktop? Para responder a isso, vamos dar uma olhada nos componentes.

Existem dois componentes para o serviço de ambiente de trabalho virtual do Windows que precisam de ser autenticados:

- O feed no cliente virtual do Windows desktop que dá aos utilizadores uma lista de ambientes de trabalho disponíveis ou aplicações a que têm acesso. Este processo de autenticação acontece no Azure Ative Directory, o que significa que este componente não é o foco deste artigo.
- A sessão RDP que resulta de um utilizador selecionar um dos recursos disponíveis. Este componente utiliza a autenticação Kerberos e requer um proxy KDC para utilizadores remotos.

Este artigo irá mostrar-lhe como configurar o feed no cliente Virtual Desktop do Windows no portal Azure. Se quiser aprender a configurar o papel rd gateway, consulte [implementar o papel de RD Gateway](/windows-server/remote/rd-gateway-role).

## <a name="requirements"></a>Requisitos

Para configurar um anfitrião de sessão virtual do Windows desktop com um proxy KDC, você precisará das seguintes coisas:

- Acesso ao portal Azure e a uma conta de administrador da Azure.
- As máquinas de clientes remotos devem estar a funcionar tanto o Windows 10 como o Windows 7 e ter o [cliente Windows Desktop](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) instalado.
- Deve ter um representante da KDC já instalado na sua máquina. Para aprender a fazê-lo, consulte [Configurar o papel RD Gateway para o Windows Virtual Desktop](rd-gateway-role.md).
- O sistema operativo da máquina deve ser o Windows Server 2016 ou mais tarde.

Assim que tiveres a certeza de que cumpres estes requisitos, estás pronto para começar.

## <a name="how-to-configure-the-kdc-proxy"></a>Como configurar o representante da KDC

Para configurar o representante da KDC:

1. Inicie sessão no portal do Azure como administrador.

2. Aceda à página de desktop virtual do Windows.

3. Selecione a piscina hospedeira para a seguinte forma ativar o proxy KDC e, em seguida, selecione **As Propriedades RDP**.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da página do portal Azure mostrando um utilizador selecionando piscinas hospedeiras, em seguida, o nome da piscina de anfitrião exemplo, em seguida, propriedades RDP.](media/rdp-properties.png)

4. Selecione o separador **Avançado** e, em seguida, introduza um valor no seguinte formato sem espaços:

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Uma imagem mostrando o separador Avançado selecionado, com o valor introduzido como descrito no passo 4.](media/advanced-tab-selected.png)

5. Selecione **Guardar**.

6. O pool de anfitriões selecionado deve agora começar a emitir ficheiros de conexão RDP que incluem o valor kdcproxyname que introduziu no passo 4.

## <a name="next-steps"></a>Passos seguintes

Para aprender a gerir o lado dos Serviços de Secretária remoto do proxy KDC e atribuir a função RD Gateway, consulte [implementar a função RD Gateway](/windows-server/remote/rd-gateway-role).

Se estiver interessado em escalar os seus servidores de procuração KDC, aprenda a configurar alta disponibilidade para proxy KDC em [Adicionar alta disponibilidade para a frente web RD Web e Gateway](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).
