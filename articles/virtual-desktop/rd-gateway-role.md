---
title: Implementar função RD Gateway Windows Virtual Desktop - Azure
description: Como implementar o papel RD Gateway no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71bd7d38727d99c05a15c54e5141c613960d9050
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99220824"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>Implementar a função RD Gateway no Windows Virtual Desktop (pré-visualização)

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo irá dizer-lhe como utilizar a função RD Gateway (pré-visualização) para implementar servidores Remote Desktop Gateway no seu ambiente. Pode instalar as funções do servidor em máquinas físicas ou máquinas virtuais, dependendo se está a criar um ambiente no local, baseado em nuvem ou híbrido.

## <a name="install-the-rd-gateway-role"></a>Instale a função RD Gateway

1. Inscreva-se no servidor alvo com credenciais administrativas.

2. No **Gestor do Servidor**, selecione **Gerir** e, em seguida, selecione **Adicionar Funções e Funcionalidades**. O **instalador Add Roles and Features** será aberto.

3. Em **Antes de começar,** selecione **Seguinte**.

4. No **Tipo de Instalação Select**, selecione a **instalação baseada em funções ou baseada em funcionalidades,** em seguida, selecione **Seguinte**.

5. Para **selecionar o servidor de destino**, **selecione um servidor a partir da piscina do servidor**. Para **Server Pool,** selecione o nome do seu computador local. Quando terminar, selecione **Next**.

6. Em **Funções de Funções de Servidor**  >  **Selecionados**, selecione **Serviços de Ambiente de Trabalho Remoto**. Quando terminar, selecione **Next**.

7. Em **Serviços de Ambiente de Trabalho Remoto**, selecione **Seguinte.**

8. Para **serviços de funções Select**, selecione apenas **Remote Desktop Gateway** Quando tiver sido solicitado para adicionar as funcionalidades necessárias, selecione **Adicionar Funcionalidades**. Quando terminar, selecione **Next**.

9. Para **a Política de Rede e Serviços de Acesso**, selecione **Seguinte**.

10. Para **a Função do Servidor Web (IIS),** selecione **Seguinte**.

11. Para **serviços de função Select**, selecione **Seguinte**.

12. Para **confirmar as seleções de instalação**, selecione **Instalar**. Não feche o instalador enquanto o processo de instalação estiver a decorrer.

## <a name="configure-rd-gateway-role"></a>Configure papel RD Gateway

Uma vez instalada a função RD Gateway, terá de a configurar.

Para configurar o papel rd gateway:

1. Abra o Gestor do **Servidor** e, em seguida, selecione **Serviços de Ambiente de Trabalho Remoto**.

2. Vá a **Servers,** clique com o botão direito no nome do seu servidor e, em seguida, selecione **RD Gateway Manager**.

3. No Gestor de Gateway RD, clique com o botão direito no nome do seu gateway e, em seguida, selecione **Propriedades**.

4. Abra o **separador certificado SSL,** selecione o **certificado de importação para a** bolha do Gateway RD e, em seguida, selecione **Browse and Import Certificate...**.

5. Selecione o nome do seu ficheiro PFX e, em seguida, selecione **Abrir**.

6. Introduza a palavra-passe para o ficheiro PFX quando solicitado.

7. Depois de importar o certificado e a sua chave privada, o visor deve apresentar os principais atributos do certificado.

>[!NOTE]
>Como o papel de RD Gateway é suposto ser público, recomendamos que use um certificado emitido publicamente. Se utilizar um certificado emitido em particular, terá de se certificar de que configura todos os clientes com a cadeia de confiança do certificado previamente.

## <a name="next-steps"></a>Passos seguintes

Se pretender adicionar uma elevada disponibilidade à sua função RD Gateway, consulte [Adicionar alta disponibilidade à frente web RD Web e Gateway.](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)