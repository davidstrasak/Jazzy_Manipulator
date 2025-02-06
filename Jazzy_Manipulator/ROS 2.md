# Jak vznikl ROS
Robotika má za cíl nahradit nějakou práci, kterou lidé musí dělat a to i v případech fyzické aktivity ale i dělání rozhodnutí.

Fyzické aktivity jsou zadaný mechanikou a elektronikou.
Dělání rozhodnutí jsou zadaný softwarem.

My tady děláme hlavně ten software. Budeme tam používat kopu frameworků, který dělají:
- Voice recognition
- Robot control
- Object Grasping
- Trajectory planning
Původně měla každá fakulta svůj vlastní software a proto byl vytvořenej ROS - framework co umožňuje tyhle věci spojit. Navíc to umožňuje kolaboraci mezi robotickýma nadšencema.
![[Pasted image 20250206174957.png]]

Pointa ROSu je aby se pořád nedělaly stejný věci dokola.
![[Pasted image 20250206175119.png]]

# ROS 2
ROS 2 umožňuje vzít si robota jako několik bloků a nastavit si to, aby to fungovalo s mým ovládacím scriptem.

Např. Mám kód, kterej byl napsanej bez ROS 2 na nějakýho robota. Je to skvělý a funguje to dobře a tak dostanu job offer a začnu pracovat na novým robotovi tak, aby měl stejnou funkcionalitu. Ten má ale jiný motory a jinej HW a tak musím celej kód přepsat.
No a tohle se bez ROS 2 děje při každé iteraci.
![[Pasted image 20250206175546.png]]
Jde o stejnej kód, ale musí se přepisovat, protože ho jakákoliv malá změna změní.

## Jak to bude vypadat s ROS 2
ROS 2 bude řešit drivery motorů, takže stačí napsat kód jednou a pro každýho toho robota se ROS 2 postará, aby to fungovalo.

# ROS 1 x ROS 2
ROS 1 je definovanej pro jednoho robota a pro jasnou komunikaci.
ROS 2 je udělanej pro mnohem víc možností - je víc vymazlenej.
![[Pasted image 20250206180223.png]]
ROS 1 byl definovanej pro drátový připojení k internetu. ROS 2 umožňuje používat WiFI.
ROS 2 umožňuje ovládat víc robotů a umožnit jim kolaboraci.
Embedded Platforms - ROS 2 umožňuje sériovou komunikaci s MCUs a tak umíme sbírat data a na základě toho dávat příkazy.
ROS 2 bere vpotaz nutnost bezpečné komunikace mezi roboty.
ROS 2 je zaměřený na realtime systémy a tak máme garantovanej čas do kdy to vypočteme.

# ROS 2 Architektura
ROS 2 je redesign ROS 1 v těchto ohledech:
DDS je industrial-grade protokol co umožňuje komunikaci mezi roboty. Je to stabilní a robustní protokol který je zavedený. Existuje víc firem co dělají implementace DDS a my si tak můžeme v ROS 2 vybrat. Jsou to:
- Cyclone DDS
- Fast DDS
- Connext DDS
Potom se vybere middleware (RMW).
Na základě toho se logika vymyslí v ROS 2 Client Library (RCL) - tohle jsou funkcionality v C a C++.
Nakonec máme možnost si zvolit vlastní programovací jazyk, ve kterým si sami napíšeme naše scripty - C++, Python a Java.
![[Pasted image 20250206180955.png]]

# Je to operační systém?
Není. Má 4 hlavní aspekty:
## Abstrakce Hardwaru
Tenhle OS umí používat hardware bez ohledu na tom, kterej to je a jak je výkonnej - Je jedno, jestli je kamera od této nebo této firmy.
Funguje to podobně jako PC - na PC si můžu zvolit operační systém a díky tomu mám třeba aplikaci dostupnou jenom pro tento OS a ta aplikace nějak využívá RAMku, CPU, GPU, ...
Stejně tak ROS 2 dává userovi standardní interface pro používání částí robota - kamery, motory, CPU, GPU, ..., díky tomu nemusíme řešit jakej máme motor nebo jaký komunikační protokoly, protože to vyřeší ROS.

## Low-level kontrola deviců
Podobně jako u PC - pokud chci používat kopírku, musím si nainstalovat drivery. Tenhle driver vytvoří komunikaci mezi PC a mezi kopírkou.
Stejně tak pokud chci u ROS robota používat kameru, výrobce kamery si vytvoří driver a ten mi umožňí vyřešit tu komunikaci mezi OS a kamerou.

## Komunikace mezi procesy
ROS 2 umožňuje komunikaci mezi různými procesy.
V ROS 2 se každýmu procesu říká node (buňka). Node by mohla být aplikace co mi naplánuje trasu a řídí.
ROS 2 má tři typy komunikace v rámci procesů:
### Topic
Publisher je node, která vyšle zprávu do kanálu.
Subscriber poslouchá a tak si tu zprávu přečte.
Publisherů a subů může být víc, protože v rámci jednoho kanálu může hodně nodů posílat i číst data. Každá node může dělat cokoliv.
Každý kanál musí mít jiný jméno.
![[Pasted image 20250206183501.png]]
### Service
Services můžou být takový funkce, který vezmou data a něco s jima udělají.
Server jsou hosti téhle funkce. Server může nabízet počítání integrálů.
Client může zavolat na server a tak mít spočítanej integrál.
Client posílá request, server potom spočítá výsledek. Zatímco client čeká, může dělat další věci. Jak server dodělá výpočet, pošle zpátky zprávu.
![[Pasted image 20250206183515.png]]
### Action
Zase máme 2 nodes. Action server nabízí tasky, který jsou delší.
Server může nabízet navigaci z bodu A do bodu B.
Client chce být navigován.
Komunikace probíhá tak, že Client pošle Goal. Action server potom provede všechny kalkulace a provádí výpočty než bude Goal splněn. Během toho může client počítat co chce dál. Action server průběžně posílá feedback ohledně progressu až nakonec pošle result.
Na základě feedbacku je u Clienta možný zrušit výpočet pomocí cancel funkce. V tom případě server pošle zprávu, že byla kalkulace úspěšně zrušená.
![[Pasted image 20250206183530.png]]

## Package management
Máme kopu packagů, který mi s instalací umožňují si přidávat pro robota funkce.
Robota si rozdělím do více packagů a každej z nich dělá specifické části. Package může být na description, simulation, control, vision, kinematics, ...
Díky tomu pokud budu používat např. stejnej sensor vzdálenosti pro mýho spiderbota, můžu si tenhle package vzít a přendat ho do drona a bude fungovat stejně a dobře.
![[Pasted image 20250206183554.png]]

# Architektura jedné ROS 2 aplikace
Už jsme si nainstalovali hodně packagů - gazebo, nav2, moveit, tf2, ros2_control, ...
Tyhle package jsou uloženy obecně ve struktuře, která se jmenuje Underlay. Je to struktura, která je obecná pro celou ROS 2 instalaci na mým PC.
Následně si z nich udělám části, který budou pohánět mýho specifickýho robota. Tohle se ukládá do část Overlay - tady jsou packages, který budou mít přednost před těmi obecnými.

V rámci jednoho package je nějakej C++ nebo Python file, Config file a Launch file.