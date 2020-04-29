---
title: Gerir dependências jar - Azure HDInsight
description: Este artigo discute as melhores práticas para gerir as dependências do Java Archive (JAR) para aplicações HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77135736"
---
# <a name="jar-dependency-management-best-practices"></a>Boas práticas de gestão da dependência jar

Os componentes instalados em clusters HDInsight têm dependências de bibliotecas de terceiros. Normalmente, uma versão específica de módulos comuns como guava é referenciada por estes componentes incorporados. Quando submete uma aplicação com as suas dependências, pode causar um conflito entre diferentes versões do mesmo módulo. Se a versão componente que refere no caminho de classe primeiro, os componentes incorporados podem lançar exceções devido à incompatibilidade da versão. No entanto, se os componentes incorporados injetarem as suas dependências `NoSuchMethod`primeiro no caminho de classe, a sua aplicação pode lançar erros como .

Para evitar conflitos de versão, considere sombreado as dependências da sua aplicação.

## <a name="what-does-package-shading-mean"></a>O que significa sombreado de pacote?
Sombreado fornece uma maneira de incluir e renomear dependências. Recoloca as classes e reescreve o bytecode e os recursos afetados para criar uma cópia privada das suas dependências.

## <a name="how-to-shade-a-package"></a>Como ensombrar um pacote?

### <a name="use-uber-jar"></a>Use uber-jar
Uber-jar é um único frasco que contém tanto o frasco de aplicação como as suas dependências. As dependências do frasco uber são por defeito não sombreadas. Em alguns casos, isto pode introduzir conflito sinuoso se outros componentes ou aplicações referenciarem uma versão diferente dessas bibliotecas. Para evitar isto, pode construir um ficheiro Uber-Jar com algumas (ou todas) dependências sombreadas.

### <a name="shade-package-using-maven"></a>Pacote de sombra usando Maven
Maven pode construir aplicações escritas tanto em Java como em Scala. Maven-shade-plugin pode ajudá-lo a criar um frasco uber-sombra facilmente.

O exemplo abaixo `pom.xml` mostra um ficheiro que foi atualizado para ensombrar um pacote usando maven-shade-plugin.  A secção `<relocation>…</relocation>` XML desloca `com.google.guava` as `com.google.shaded.guava` classes do pacote para a embalagem, movendo as entradas correspondentes de ficheiros JAR e reescrevendo o código de acesso afetado.

Depois `pom.xml`de mudar, `mvn package` pode executar para construir o frasco de uber sombreado.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Pacote de sombra usando SBT
SBT é também uma ferramenta de construção para Scala e Java. O SBT não tem um plugin de sombra como o plugin de sombra maven. Pode modificar `build.sbt` o ficheiro para pacotes de sombra. 

Por exemplo, `com.google.guava`para sombra, pode adicionar `build.sbt` o comando abaixo ao ficheiro:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Depois podes `sbt clean` `sbt assembly` correr e construir o ficheiro de frascos sombreados. 

## <a name="next-steps"></a>Passos seguintes

* [Utilize ferramentas de intelliJ HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Criar um pedido de Scala Maven para a Spark in IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
