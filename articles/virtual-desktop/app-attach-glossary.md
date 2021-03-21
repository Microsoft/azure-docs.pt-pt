---
title: Aplicativo Windows Virtual Desktop MSIX anexa glossário - Azure
description: Um glossário de aplicações MSIX anexa termos e conceitos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7132eae073f3d53a104536076ae801ec9ff93e5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96518673"
---
# <a name="msix-app-attach-glossary"></a>App MSIX anexa glossário

Este artigo é uma lista de definições para termos e conceitos chave relacionados com o anexo de aplicações MSIX.

## <a name="msix-container"></a>Recipiente MSIX

Um recipiente MSIX é onde as aplicações MSIX são executadas. Para saber mais, consulte [os recipientes MSIX.](/windows/msix/msix-container)

## <a name="msix-application"></a>Aplicação MSIX 

Uma aplicação armazenada num . Ficheiro MSIX.

## <a name="msix-package"></a>Pacote MSIX 

Um pacote MSIX é um ficheiro ou aplicação MSIX.

## <a name="msix-share"></a>Partilha da MSIX

Uma participação da MSIX é uma partilha de rede que detém pacotes MSIX expandidos. As ações da MSIX suportam sMB 3 ou mais tarde. As aplicações são encenadas a partir desta partilha msix sem ter que mover ficheiros de aplicações para a unidade do sistema.

## <a name="msix-image"></a>Imagem MSIX

Uma imagem MSIX é um ficheiro VHD, VHDx ou CIM que contém uma ou mais aplicações embaladas msix. Cada aplicação é entregue na imagem MSIX utilizando a ferramenta MSIXMGR.

## <a name="repackage"></a>Reembalagem

A reembalagem requer uma aplicação não-MSIX e converte-a em MSIX utilizando a Ferramenta de Embalagem MSIX (MPT). Para mais informações, consulte a [visão geral da Ferramenta de Embalagem MSIX.](/windows/msix/packaging-tool/tool-overview)

## <a name="expand-an-msix-package"></a>Expandir um pacote MSIX

Expandir um pacote MSIX é um processo em várias etapas. A expansão pega no ficheiro MSIX e coloca o seu conteúdo num ficheiro VHD(x) ou CIM. 

Para expandir um pacote MSIX:

1. Obtenha um pacote MSIX (ficheiro MSIX).
2. Mude o nome do ficheiro MSIX para um ficheiro .zip.
3. Desaperte o ficheiro .zip resultante numa pasta.
4. Crie um VHD do mesmo tamanho que a pasta.
5. Monte o VHD.
6. Inicialize um disco.
7. Criar uma divisória.
8. Formate a partição.
9. Copie o conteúdo desapertado no VHD.
10. Utilize a ferramenta MSIXMGR para aplicar ACLs no conteúdo do VHD.
11. Desmonte o VHD(x) ou [o CIM](#cim).

## <a name="upload-an-msix-package"></a>Faça upload de um pacote MSIX 

O upload de um pacote MSIX envolve o upload do VHD(x) ou [CIM](#cim) que contém um pacote MSIX expandido para a partilha MSIX.

No Windows Virtual Desktop, os uploads acontecem uma vez por partilha MSIX. Uma vez que você carrega um pacote, todos os pools anfitriões na mesma subscrição podem fazê-lo referenciar.

## <a name="add-an-msix-package"></a>Adicione um pacote MSIX

No Windows Virtual Desktop, adicionar um pacote MSIX liga-o a uma piscina hospedeira.

## <a name="publish-an-msix-package"></a>Publicar um pacote MSIX 

No Windows Virtual Desktop, um pacote MSIX publicado deve ser atribuído a um Serviço de Domínio de Diretório Ativo (AD DS) ou a um grupo de utilizadores do Azure Ative Directory (Azure AD).

## <a name="staging"></a>Processo de teste

A encenação envolve duas coisas:

- Montagem do VHD(x) ou [CIM](#cim) no VM.
- Notificando o SO de que o pacote MSIX está disponível para inscrição.

## <a name="registration"></a>Registo

O registo disponibiliza um pacote MSIX encenado para os seus utilizadores. O registo é por utilizador. Se não tiver registado explicitamente uma aplicação para esse utilizador específico, não poderá executar a aplicação.

Existem dois tipos de registo: regular e atrasado.

### <a name="regular-registration"></a>Inscrição regular

No registo regular, cada pedido atribuído a um utilizador está totalmente registado. O registo ocorre durante o tempo em que o utilizador entra na sessão, o que pode ter impacto no tempo que demora a começar a utilizar o Windows Virtual Desktop.

### <a name="delayed-registration"></a>Inscrição atrasada

No registo atrasado, cada pedido atribuído ao utilizador está apenas parcialmente registado. Registo parcial significa que as associações de ficheiros do menu Iniciar e de duplo clique estão registadas. O registo ocorre enquanto o utilizador entra na sessão, pelo que tem um impacto mínimo no tempo que demora a começar a utilizar o Windows Virtual Desktop. O registo só termina quando o utilizador executa a aplicação no pacote MSIX.

O registo atrasado é atualmente a configuração padrão para o anexo de aplicações MSIX.

## <a name="deregistration"></a>Desregistração

O cancelamento de registo remove um pacote MSIX registado mas não funcionando para um utilizador. O cancelamento do registo acontece enquanto o utilizador assina fora da sessão. Durante o cancelamento do registo, a app MSIX anexa os dados da aplicação específicos do utilizador para o perfil do utilizador local.

## <a name="destage"></a>Destage

O Destaging notifica o SO de que um pacote ou aplicação MSIX que não está atualmente em execução e não é encenada para qualquer utilizador pode ser desmontada. Isto remove todas as referências a ele no SISTEMA.

## <a name="cim"></a>CIM

. A CIM é uma nova extensão de ficheiros associada ao Sistema de Ficheiros de Imagem Compósito (CimFS). A montagem e desmontagem de ficheiros CIM é mais rápida que os ficheiros VHD. A CIM também consome menos CPU e memória do que o VHD.

Um ficheiro CIM é um ficheiro com um . Extensão CIM que contém metadados e pelo menos seis ficheiros adicionais que contêm dados reais. Os ficheiros dentro do ficheiro CIM não têm extensões. A tabela a seguir é uma lista de ficheiros de exemplo que encontraria dentro de uma CIM:

| Nome de ficheiro | Extensão | Tamanho |
|-----------|-----------|------|
| VSC | CIM | 1 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | ND | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | ND | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | ND | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | ND | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | ND | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | ND | 264,132 KB |

O quadro a seguir é uma comparação de desempenho entre VHD e CimFS. Estes números foram o resultado de um teste com 500 ficheiros MB em cada formato executado numa máquina DSv4.

|  Especificações                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Tempo médio de montagem     | 356 ms                     | 255 ms      |
| Tempo médio de desmontagem   | 1615 ms                    | 36 ms       |
| Consumo de memória | 6% (de 8 GB)                      | 2% (de 8 GB)       |
| CPU (pico de contagem)          | Esgotado várias vezes | Sem impacto |

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre o anexo de aplicações MSIX, consulte a nossa [visão geral](what-is-app-attach.md) e [as FAQ.](app-attach-faq.md) Caso contrário, inicie com [o anexo de aplicações Configurar.](app-attach.md)
