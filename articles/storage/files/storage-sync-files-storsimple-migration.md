---
title: Migrar do StorSimple para o Sincronização de Arquivos do Azure
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: O StorSimple é um produto de fim de vida e Sincronização de Arquivos do Azure é a solução para a qual migrar. Saiba mais sobre o conceito de migração e entre em contato com a AzureFiles@microsoft.com para obter ajuda de migração personalizada.
ms.openlocfilehash: 1cc88080522a62085d9a515223512ef25c20a9e4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895086"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>Migração do StorSimple para Sincronização de Arquivos do Azure

O StorSimple é um produto descontinuado da Microsoft. O suporte estendido para este produto e seu serviço de nuvem durará até o final de 2022.
É importante começar a planejar uma migração do StorSimple imediatamente.

Os dispositivos StorSimple do serviço do Azure de longo prazo estratégico e padrão podem ser migrados para o, é Sincronização de Arquivos do Azure. Ele é um serviço do Azure geralmente disponível com um superconjunto de recursos no StorSimple.

## <a name="full-cloud-side-migration-with-limited-downtime"></a>Migração completa no lado da nuvem com tempo de inatividade limitado
Este artigo descreve o conceito de como uma migração começará.
É imperativo observar que os clientes que precisam de migração do StorSimple e para o Sincronização de Arquivos do Azure não precisam continuar por conta própria.

> [!IMPORTANT]
> A Microsoft está comprometida em auxiliar os clientes em sua migração. Envie um email AzureFiles@microsoft. com para um plano de migração personalizado, bem como assistência durante a migração.

## <a name="migration-approach"></a>Abordagem de migração
A migração para Sincronização de Arquivos do Azure começará no lado da nuvem com impacto mínimo sobre o tempo de inatividade local e limitado.
O conceito abaixo destina-se a dispositivos StorSimple da série 8000.
Se você estiver em necessidade de migração do StorSimple 7000 Series, a primeira etapa envolve uma atualização gratuita para um dispositivo de empréstimo de série 8000 correspondente da Microsoft.
Entre em contato com AzureFiles@microsoft.com e ajudaremos você a organizar um número apropriado de dispositivos de empréstimo.

### <a name="general-approach"></a>Abordagem geral
![Pressionando](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "Ilustrando a migração do lado da nuvem por meio de um dispositivo virtual temporário e do Windows Server para um novo Windows Server local, substituindo o dispositivo StorSimple local")

1. Faça um clone de volume do seu dispositivo StorSimple local e monte-o em um dispositivo virtual StorSimple temporário.
2. Conecte o dispositivo virtual via iSCSI a uma VM do Azure temporária.
3. Instalar Sincronização de Arquivos do Azure na VM do Windows Server temporária-uma chave de Registro específica para essa migração também precisa ser definida antes de a sincronização ser configurada no servidor.
    * Dependendo do número de compartilhamentos em seu volume do StorSimple, implante o mesmo que vários compartilhamentos de arquivos do Azure. (É recomendável implantar um compartilhamento de arquivos do Azure por conta de armazenamento.)
    * Configure a sincronização entre o compartilhamento individual na VM de nuvem do Windows Server e um compartilhamento de arquivos do Azure. (mapeamento 1:1)
    * Opcionalmente, adicione um servidor local como um cache de desempenho no local, com a camada de nuvem habilitada. Essa etapa será necessária se você quiser substituir seu StorSimple local por um recurso mais rico, cache local, equipado com o Windows Server e a camada de nuvem do Sincronização de Arquivos do Azure. O Windows Server local pode ser um computador físico ou um cluster ou uma máquina virtual. Ele não precisa ter tanto armazenamento implantado quanto o tamanho do conjunto de espaço. Ele só precisa de armazenamento suficiente para armazenar localmente em cache os arquivos acessados com mais frequência.

## <a name="minimizing-downtime"></a>Minimizando o tempo de inatividade
Após a etapa 3 acima, o dispositivo local do StorSimple ainda é usado ativamente por usuários e aplicativos. Portanto, o conjunto de arquivos sincronizados a partir do clone de volume inicial está ligeiramente desatualizado no momento em que a sincronização é concluída.
A abordagem para minimizar o tempo de inatividade é repetir o processo de sincronização a partir do clone de volume, de modo que a sincronização seja concluída mais rápido e rápida com cada iteração, que por sua vez é habilitada pelas alterações entre os clones de volume se tornarem menos ou menos.
Você pode repetir esse processo até que a sincronização de um clone de volume possa ser concluída na quantidade de tempo que você encontrar aceitável para inatividade.
Quando esse for o caso, impeça que usuários e aplicativos façam alterações no seu dispositivo StorSimple. O tempo de inatividade começa.
Faça outro clone de volume e deixe que ele seja sincronizado com os servidores conectados.
Estabeleça acesso a seus usuários e aplicativos para o novo, Sincronização de Arquivos do Azure com suporte do Windows Server.
Considere implantar/ajustar um namespace de DFS para fazer a redução do antigo dispositivo StorSimple para o novo Windows Server transparente para aplicativos e usuários.
A migração foi concluída.

## <a name="migration-goal"></a>Meta de migração
Após a conclusão da migração, o dispositivo virtual StorSimple temporário e a VM do Azure podem ser desprovisionados.

Além disso, o dispositivo local do StorSimple pode ser desprovisionado, pois os usuários e aplicativos já estão acessando o Windows Server.
O que você está usando é representado na imagem abaixo. Uma implantação de Sincronização de Arquivos do Azure padrão apresenta vários compartilhamentos de arquivos do Azure e servidores Windows conectados a eles por meio de Sincronização de Arquivos do Azure. Lembre-se de que um único servidor pode conectar diferentes pastas locais a diferentes compartilhamentos de arquivos ao mesmo tempo.
Além disso, um compartilhamento de arquivos do Azure pode ser sincronizado com vários servidores diferentes, caso você precise de dados armazenados em cache em filiais. Verifique também se você pode otimizar suas políticas de camadas de nuvem para um uso mais eficiente do seu espaço de armazenamento local.

![Pressionando](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "Uma ilustração que mostra a meta após a conclusão da migração. Ele descreve vários compartilhamentos de arquivos sincronizando para um Windows Server local com usuários e aplicativos que acessam arquivos na nuvem ou no Windows Server.")

## <a name="next-steps"></a>Passos seguintes
Familiarize-se com os arquivos e Sincronização de Arquivos do Azure do Azure. É importante entender a terminologia de Sincronização de Arquivos do Azure e o padrão de implantação para uma migração bem-sucedida. Há informações mais detalhadas disponíveis para cada etapa neste artigo de visão geral. Certifique-se de entrar em contato com a Microsoft para obter ajuda personalizada durante o planejamento e a execução da sua migração.

> [!IMPORTANT]
> A Microsoft está comprometida em auxiliar os clientes em sua migração. Envie um email AzureFiles@microsoft. com para um plano de migração personalizado, bem como assistência durante a migração.

## <a name="additional-resources"></a>Recursos adicionais
Sincronização de Arquivos do Azure, como o serviço de destino, tem dois documentos fundamentais que recomendamos que você leia, se você for novo no Sincronização de Arquivos do Azure.
* [Sincronização de Arquivos do Azure-visão geral](storage-sync-files-planning.md)
* [Sincronização de Arquivos do Azure-guia de implantação](storage-sync-files-deployment-guide.md)

O arquivos do Azure é um serviço de armazenamento no Azure, oferecendo compartilhamentos de arquivos como um serviço. Não é necessário pagar ou manter uma VM ou um armazenamento de VM associado.
* [Arquivos do Azure-visão geral](storage-files-introduction.md)
* [Arquivos do Azure-como implantar um compartilhamento de arquivos do Azure](storage-how-to-create-file-share.md)