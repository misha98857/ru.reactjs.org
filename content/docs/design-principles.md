---
id: design-principles
title: Принципы проектирования React
layout: contributing
permalink: docs/design-principles.html
prev: implementation-notes.html
redirect_from:
  - "contributing/design-principles.html"
---

Мы написали этот документ, чтобы у вас было лучшее представление о том, как мы решаем, что делает React, а что нет, и какова наша философия разработки. Хоть мы и рады видеть вклад сообщества, мы вряд ли выберем путь, который нарушает один или несколько из этих принципов.

>**Примечание:**
>
>Эта статья подразумевает глубокое понимание React. В ней описаны концепции разработки *самого React*, но не React-компонентов или приложений.
>
>Для знакомства с React почитайте главу [Философия React](/docs/thinking-in-react.html).

### Композиция {#composition}

Ключевая возможность React — это композиция компонентов. Компоненты, написанные разными людьми, должны хорошо работать вместе. Для нас важно, чтобы вы могли добавлять в компонент функциональность, не вызывая волну изменений по всему коду.

Например, должна быть возможность вводить некое внутреннее состояние в компонент без изменения зависящих от него компонентов. Также должна быть возможность добавлять при необходимости код инициализации и разрушения в любой компонент.

Нет ничего «плохого» в использовании состояния или методов жизненного цикла в компонентах. Как и любую мощную возможность, их стоит использовать в меру, но мы не собираемся их удалять. Напротив, мы считаем, что они являются неотъемлемой частью того, что делает React полезным. Возможно, мы добавим [больше паттернов функционального программирования](https://github.com/reactjs/react-future/tree/master/07%20-%20Returning%20State) в будущем, но как внутреннее состояние, так и методы жизненного цикла будут частью этой модели.

Компоненты часто описываются как «просто функции», но, по нашему мнению, они должны быть чем-то большим, чтобы быть полезными. Компоненты в React описывают поведение, которое можно композировать. Эта идея распространяется в том числе на рендеринг, жизненный цикл и состояние. Некоторые сторонние библиотеки, вроде [Relay](https://facebook.github.io/relay/), дополняют компоненты другими возможностями, например, описанием зависимостей данных. Вполне возможно, что эти идеи могут попасть в React в той или иной форме.

### Общая абстракция {#common-abstraction}

В целом, мы [против добавления функциональности](https://www.youtube.com/watch?v=4anAwXYqLG8), которая может быть реализована в пользовательских приложениях. Мы не хотим раздувать ваше приложение бесполезным кодом библиотеки. Но и у этого правила есть свои исключения.

Например, если бы React не предоставлял поддержку внутреннего состояния или методов жизненного цикла, люди бы создавали собственные абстракции для них. Когда есть несколько конкурирующих абстракций, React не может применять или использовать их свойства. Для работы React нужен некий наименьший общий знаменатель.

Вот почему иногда мы добавляем возможности в сам React. Если мы замечаем, что какая-то возможность реализуется плохо совместимым или неэффективным способом во многих компонентах, мы можем захотеть внедрить эту возможность в React. Мы подходим к таким изменениям со всей серьёзностью. 
Если мы добавляем новую возможность, значит мы уверены, что повышение уровня абстракции принесёт выгоду всей экосистеме. Хорошие примеры для этого — состояние, методы жизненного цикла, кросс-браузерная нормализация событий.

Мы всегда обсуждаем с сообществом такие предложения по улучшению. Некоторые из этих обсуждений можно найти по метке [«big picture»](https://github.com/facebook/react/issues?q=is:open+is:issue+label:"Type:+Big+Picture") в трекере задач React.

### Лазейки {#escape-hatches}

React прагматичен. Это обусловлено потребностями продуктов, написанных в Facebook. Хоть на него и влияют некоторые не самые популярные парадигмы, такие как функциональное программирование, цель проекта — оставаться доступным для широкого круга разработчиков с разным уровнем опыта и навыками.

Если мы хотим отказаться от паттерна, который нам не нравится, на нас лежит ответственность рассмотреть все существующие варианты его использования и [проинформировать сообщество об альтернативах](/blog/2016/07/13/mixins-considered-harmful.html), прежде чем отказаться от него. Если какой-то полезный для создания приложений паттерн трудно описать декларативно, мы [предоставим для него императивный API](/docs/more-about-refs.html). Если мы не можем найти идеальный API для того, что мы считаем необходимым во многих приложениях, мы [предоставим временный, частично работающий API](/docs/legacy-context.html), при условии, что от него можно будет избавиться позже. Это оставляет открытой дверь для будущих улучшений.

### Стабильность {#stability}

Мы ценим стабильность API. У нас в Facebook более 50 тысяч компонентов, использующих React. Многие другие компании, включая [Twitter](https://twitter.com/) и [Airbnb](https://www.airbnb.com/), также активно используют React. Поэтому мы обычно нехотя меняем публичный API или поведение.

Однако, мы считаем переоценённой идею, что стабильность — это когда «ничего не меняется». Она быстро приводит к застою. Вместо этого, мы предпочитаем воспринимать стабильность в смысле «это много используется в продакшене и когда что-то меняется, существует чёткий (и желательно автоматизированный) план миграции».

Когда мы объявляем паттерн устаревшим, мы сперва изучаем как он используется внутри Facebook и добавляем предупреждения об устаревании. Это позволяет нам оценить масштабы последствий изменения. Иногда мы отменяем изменение, если видим, что ещё слишком рано приступать к его воплощению и нам нужно продумать стратегию продвижения кодовой базы к состоянию готовности.

Если мы уверены, что изменение не слишком большое и для всех случаев использования возможна миграция, то предупреждение об устаревании публикуется перед OSS-сообществом. Мы тесно общаемся со многими React-пользователями вне Facebook, следим за популярными OSS-проектами и помогаем им исправлять устаревший код.

Учитывая огромный размер кодовой базы React в Facebook, успешная внутренняя миграция часто является хорошим индикатором того, что в других компаниях также не будет проблем. Тем не менее, люди иногда указывают на неучтённые варианты использования и мы добавляем лазейки или пересматриваем подход.

Ничто не объявляется устаревшим без веской причины. Мы понимаем, что иногда предупреждения об устаревании разочаровывают. Но они вводятся, так как это открывает дорогу улучшениям и новым возможностям, которые мы и сообщество считаем важными.

Например, мы добавили [предупреждение о неизвестных DOM-свойствах](/warnings/unknown-prop.html) в React 15.2.0. Этим мы затронули многие проекты. Однако, исправление этого предупреждения важно для добавления в React поддержки [пользовательских атрибутов](https://github.com/facebook/react/issues/140). Подобная причина стоит за каждым объявлением об устаревании, которое мы добавляем.

При добавлении предупреждения об устаревании, мы оставляем его пока актуальна текущая мажорная версия, [изменяя поведение только в следующей мажорной версии](/blog/2016/02/19/new-versioning-scheme.html). Если изменение создаёт много повторяющейся ручной работы, мы публикуем [codemod-скрипт](https://www.youtube.com/watch?v=d0pOgY8__JM), который автоматизирует большую часть изменений. Codemod-скрипты дают нам возможность двигаться вперёд, не закапываясь в куче кода. Рекомендуем вам тоже их использовать.

Вы можете найти ранее опубликованные codemod-скрипты в [react-codemod](https://github.com/reactjs/react-codemod) репозитории.

### Совместимость {#interoperability}

Мы придаём большое значение совместимости с существующими системами и возможности постепенного внедрения. В Facebook есть много кода, написанного не на React. Сайт использует смесь из  XHP — системы серверных компонент, внутренних UI-библиотек, которые пришли до React, и самого React. Для нас важно, чтобы любая продуктовая команда могла [начать использовать React для небольшой возможности](https://www.youtube.com/watch?v=BF58ZJ1ZQxY), а не делать ставку на переписывание своего кода.

По этой причине React предоставляет лазейки для работы с изменяемыми моделями и пытается хорошо работать вместе с другими UI-библиотеками. Вы можете обернуть существующий императивный UI в декларативный компонент и наоборот. Это очень важно для постепенного внедрения.

### Планирование {#scheduling}

Даже если компоненты описаны как функции, при использовании React они не вызываются напрямую. Каждый компонент возвращает [описание того, что должно быть отрендерено](/blog/2015/12/18/react-components-elements-and-instances.html#elements-describe-the-tree). Описание может включать как пользовательские компоненты, такие как `<LikeButton>`, так и платформо-зависимые компоненты, такие как `<div>`. В дальнейшем в какой-то момент React может «развернуть» `<LikeButton>` и фактически применить изменения к UI-дереву в соответствии с результатами рекурсивного рендеринга компонентов.

Это тонкое, но сильное различие. Поскольку вы не вызываете этот функциональный компонент, а позволяете React вызывать его, это означает, что React может отложить вызов при необходимости. В текущей реализации React рекурсивно обходит дерево и вызывает функции рендера всего обновлённого дерева за один проход. Но в будущем он может начать [задерживать некоторые обновления, чтобы избежать потери кадров](https://github.com/facebook/react/issues/6170).

Это частая тема в архитектуре React. Некоторые популярные библиотеки реализуют подход «прослушивание», когда вычисления выполняются при появлении новых данных. React, наоборот, придерживается подхода «опрашивание», когда вычисления могут быть запрошены при необходимости.

React не является универсальной библиотекой обработки данных. Это библиотека для создания пользовательских интерфейсов. Мы считаем, что приложение должно знать какие вычисления сейчас актуальны, а какие нет.

Если что-то находится вне экрана, можно отложить любую связанную с этим логику. Если данные поступают быстрее, чем кадры успевают обновиться, можно объединить их и обновлять пакетами. Мы можем приоритизировать работу, вызванную пользовательским взаимодействием (например, анимация нажатия кнопки), над менее важной фоновой работой (например, рендеринг только что загруженного из сети компонента), чтобы избежать потери кадров.

Уточним, что это на сегодняшний день ещё не реализовано. Однако, подобная свобода иллюстрирует, почему мы предпочитаем контролировать планирование и почему функция `setState()` работает асинхронно. Концептуально мы думаем об этом как о «планировании обновления».

Нам будет сложнее контролировать планирование, если мы позволим пользователям использовать подход «выталкивание при наличии данных», распространённый в [функциональном реактивном программировании](https://ru.wikipedia.org/wiki/%D0%A0%D0%B5%D0%B0%D0%BA%D1%82%D0%B8%D0%B2%D0%BD%D0%BE%D0%B5_%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5#%D0%A4%D1%83%D0%BD%D0%BA%D1%86%D0%B8%D0%BE%D0%BD%D0%B0%D0%BB%D1%8C%D0%BD%D0%BE%D0%B5_%D1%80%D0%B5%D0%B0%D0%BA%D1%82%D0%B8%D0%B2%D0%BD%D0%BE%D0%B5_%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B). Мы хотим, чтобы наш код был «связующим».

Ключевая задача для React — минимизировать количество пользовательского кода, выполняемого перед возвращением обратно в React. Это гарантирует, что React сохранит возможность планировать и разбивать работу на части в соответствии с тем, что ему известно о UI.

В команде есть внутренняя шутка, что React должен был называться «Планировщик», потому что React не хочет быть полностью «реактивным».

### Удобство разработки {#developer-experience}

Нам важно сделать процесс разработки комфортным.

Например, мы поддерживаем расширение браузера [React DevTools](https://github.com/facebook/react/tree/main/packages/react-devtools), при помощи которого можно просматривать дерево React-компонентов в Chrome и Firefox. Мы часто слышим, что это повышает производительность как инженеров Facebook, так и остального сообщества.

Мы также стараемся сделать всё возможное, чтобы предоставить полезные предупреждения для разработчиков. Например, во время разработки React предупреждает вас, если теги вложены непонятным для браузера образом, или если в API сделана опечатка. Предупреждения и связанные с ними проверки являются основной причиной, почему версия React для разработчиков медленнее, чем продакшен-версия.

Наблюдение за тем, как React используется внутри Facebook помогает нам выявить и понять наиболее частые ошибки, а также как предотвратить их заранее. Когда мы добавляем новые возможности, мы стараемся предугадать частые ошибки и предупредить о них.

Мы всегда ищем способы сделать разработку удобнее, поэтому с радостью обсуждаем ваши идеи и принимаем вклады, приносящие улучшения.

### Отладка {#debugging}

Когда что-то идёт не так, важно иметь возможность найти источник ошибки в кодовой базе. В React в этом помогают пропсы и состояние.

Если вы видите, что на экране что-то не так, можете открыть React DevTools, найти компонент, отвечающий за рендеринг, а затем проверить правильность его пропсов и состояния. Если они в порядке, значит проблема в `render()` функции компонента или в какой-то функции, вызываемой в `render()`. Проблема изолирована.

Если состояние неверно, значит проблему спровоцировал один из вызовов `setState()` в этом файле. Её также относительно просто найти и исправить, так как в одном файле обычно всего несколько вызовов `setState()`.

Если пропсы неверны, вы можете пройти в инспекторе вверх по дереву, в поисках первого компонента, который передал неправильные пропсы вниз.

Для React очень важна возможность отследить любой UI до данных, на которых он построен, в виде текущих пропсов и состояния. Одной из явных целей в нашем дизайне является то, чтобы состояние не «пропадало» в замыканиях и вычислениях, и было доступно напрямую в React.

Хотя UI динамичен, мы считаем, что синхронный рендер пропсов и состояния превращают отладку из гадания в скучный, но конечный процесс. Мы хотели бы сохранить это ограничение в React, хотя это усложняет некоторые варианты использования, такие как сложная анимация.

### Конфигурация {#configuration}

Мы считаем, что возможность глобальной конфигурации во время выполнения, может стать проблемой.

Например, иногда нас просят реализовать такую функцию, как `React.configure(options)` или `React.register(component)`. Но это создаёт множество проблем, и мы не знаем хороших решений для них.

Что, если кто-то вызовет такую функцию из сторонней библиотеки компонентов? Что, если одно React-приложение встраивает другое React-приложение и нужные им конфигурации несовместимы? Как сторонний компонент может указать, что ему нужна определённая конфигурация? Мы думаем, что глобальная конфигурация плохо работает с композицией. Поскольку композиция для React первостепенна, мы не предоставляем возможности глобальной конфигурации в коде.

Однако, мы предоставляем некоторые глобальные настройки на уровне сборки. Например, мы предоставляем отдельные сборки для разработки и продакшена. В будущем мы можем [добавить профилирующую сборку](https://github.com/facebook/react/issues/6627). Мы также открыты для рассмотрения других флагов сборки.

### За пределами DOM {#beyond-the-dom}

Мы видим ценность React в том, что он позволяет писать компоненты, в которых меньше ошибок и которые хорошо сочетаются друг с другом. Изначальной целью рендеринга для React является DOM, но [React Native](https://reactnative.dev/) не менее важен, как для Facebook, так и для сообщества.

Быть независимым от визуализации — важное ограничение в дизайне React. Это создаёт некоторые накладные расходы во внутренних представлениях. С другой стороны, любые улучшения ядра переносятся между платформами.

Наличие единой модели программирования позволяет нам создавать инженерные команды вокруг продуктов, а не платформ. Пока компромисс того стоил.

### Реализация {#implementation}

Мы стараемся предоставлять изящный API везде, где это возможно. Но мы гораздо меньше обеспокоены изящностью реализации. Реальный мир далёк от идеала и мы предпочитаем добавлять некрасивый код в библиотеку в разумной степени, если это означает, что пользователю не нужно его писать. Когда мы оцениваем новый код, мы смотрим на правильность реализации, производительность и удобство разработки. Изящность вторична.

Мы предпочитаем скучный код умному. Код одноразовый и часто меняется. Поэтому важно, чтобы он [не привносил новых внутренних абстракций без крайней необходимости](https://youtu.be/4anAwXYqLG8?t=13m9s). Подробный код, который легко перемещать, изменять и удалять, предпочтительнее изящного, преждевременно абстрагированного и трудно изменяемого кода.

### Оптимизирован для инструментов {#optimized-for-tooling}

Некоторые часто используемые API имеют длинные имена. Например, мы [специально](https://github.com/reactjs/react-future/issues/40#issuecomment-142442124) используем `componentDidMount()` вместо `didMount()` или `onMount()`. Цель в том, чтобы сделать точки взаимодействия с библиотекой хорошо заметными.

В такой большой кодовой базе, как у Facebook, очень важно иметь возможность поиска использования определённых API. Мы ценим разные длинные имена, особенно для функциональности, которую следует использовать редко. Например, `dangerouslySetInnerHTML` трудно пропустить на кодревью.

Оптимизация для поиска также важна, так как мы зависим от [codemod](https://www.youtube.com/watch?v=d0pOgY8__JM)-скриптов для внесения критических изменений. Мы хотим, чтобы было легко и безопасно вносить большие автоматизированные изменения в кодовую базу, а уникальные длинные имена нам в этом помогают. Подобно этому, уникальные имена позволяют легко создавать собственные [правила анализа](https://github.com/yannickcr/eslint-plugin-react) использования React, не беспокоясь о возможных ложных срабатываниях.

Аналогичную роль играет [JSX](/docs/introducing-jsx.html). Хотя он необязателен для работы React, мы широко используем его в Facebook как по эстетическим, так и по практическим соображениям.

В нашей кодовой базе JSX предоставляет однозначную подсказку инструментам, что они работают с деревом React-элементов. Это позволяет добавлять оптимизации во время сборки, такие как [всплытие неизменяющихся элементов](https://babeljs.io/docs/en/babel-plugin-transform-react-constant-elements/), безопасный анализ и использование внутреннего компонента codemod, а также добавление в предупреждения [пути до исходников JSX кода](https://github.com/facebook/react/pull/6771).

### Использование React в Facebook {#dogfooding}

Мы стараемся решать проблемы, поднятые сообществом. Однако, скорее всего, мы будем отдавать приоритет тем проблемам, с которыми люди *также* сталкиваются внутри Facebook. Возможно нелогично, но мы думаем, что это основная причина, по которой сообщество может полагаться на React.

Интенсивное внутреннее использование даёт нам уверенность в том, что React не исчезнет завтра. React был создан в Facebook для решения своих проблем. Он приносит ощутимую бизнес-ценность компании и используется во многих её продуктах. Использование своего продукта у себя позволяет оставаться нашему видению проекта ясным и сфокусированным на движении вперёд.

Это не означает, что мы игнорируем вопросы, поднятые сообществом. Например, мы добавили поддержку [web-компонентов](/docs/webcomponents.html) и [SVG](https://github.com/facebook/react/pull/6243) в React, хотя мы не используем их внутри. Мы активно [следим за вашими проблемами](https://github.com/facebook/react/issues/2686) и [устраняем их](/blog/2016/07/11/introducing-reacts-error-code-system.html) по мере наших возможностей. Сообщество — это то, что делает React особенным для нас и мы рады внести свой вклад.

Выпустив много OSS-проектов в Facebook, мы поняли, что попытка сделать всех счастливыми одновременно приводит к созданию слишком общих проектов, которые плохо развиваются. Мы также обнаружили, что выбор узкой аудитории и фокусировка ней, напротив приносит положительный конечный результат. Именно это мы сделали с React. И до сих пор решение проблем, с которыми сталкиваются продуктовые команды Facebook, хорошо транслировались на OSS-сообщество.

Недостатком этого подхода является то, что иногда мы не уделяем достаточного внимания тем вещам, с которыми командам Facebook не приходится сталкиваться, такими как опыт «начала работы». Мы хорошо знаем об этой проблеме. И думаем над тем как сделать лучше так, чтобы это принесло пользу всем в сообществе, не совершая тех же ошибок, которые мы делали в OSS-проектах до этого.
