---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016139"
---
1. Instalar dapl, rdmacm, ibverbs e mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. Em /etc/waagent.conf, permitir RDMA descoduando as seguintes linhas de configuração. Precisa de acesso à raiz para editar este ficheiro.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Adicione ou altere as seguintes definições de memória em KB no ficheiro /etc/security/limits.conf. Precisa de acesso à raiz para editar este ficheiro. Para efeitos de teste, pode definir o memlock para ilimitado. Por exemplo: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Instale a Biblioteca Intel MPI. Ou [compra e descarrega](https://software.intel.com/intel-mpi-library/) a biblioteca da Intel ou descarrega a [versão de avaliação gratuita.](https://registrationcenter.intel.com/en/forms/?productid=1740)

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Apenas os tempos de execução do INTEL MPI 5.x são suportados.
 
   Para etapas de instalação, consulte o [Guia de Instalação da Biblioteca Intel MPI](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Ativar os processos de não-depuração sem raiz (necessários para as versões mais recentes do INTEL MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
