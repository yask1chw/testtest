# Отчёт об исправлении сайта хелп18.рф

## Найденные причины

- Клиентское приложение использовало createRoot, исходный index.html содержал пустой root. Отдельные HTML для маршрутов отсутствовали; текст и метаданные появлялись лишь после JavaScript.
- Canonical, Open Graph, robots и sitemap ссылались на example.com; favicon /vite.svg отсутствовал.
- Серверная конфигурация не входила в архив: маршруты BrowserRouter не имели файлов для прямого открытия и обновления.
- ScrollManager уже существовал, но CSS scroll-behavior:smooth мешал мгновенному сбросу позиции. Теперь прокрутка сбрасывается в layout-effect, автоматическое восстановление браузером отключается на время работы компонента; якоря сохранены.

## Результат

18 публичных страниц с отдельными index.html и полным содержимым React-компонентов внутри root: главная, каталог услуг, 8 услуг, цены, врачи, о клинике, отзывы, статьи, контакты с реквизитами, политика, платные услуги. Отдельных опубликованных статей, категорий, карточек объектов, вложенных маршрутов или страницы реквизитов в архиве нет. Анонсы будущих статей сохранены на /stati/ без вымышленных публикаций.

Дополнительно созданы 404.html с noindex и технический HTML-переход со старого адреса политики; на Apache этот адрес возвращает 301. Эти два документа исключены из sitemap. У 18 публичных страниц уникальные title и description, canonical на HTTPS punycode-домене, index, follow. FAQ-вопросы и ответы присутствуют в исходной разметке, JSON-LD FAQ также добавлен при генерации. Существующие MedicalClinic и клиентские BreadcrumbList сохранены.

18 уникальных URL в sitemap.xml; urls-for-indexing.txt содержит тот же список. Домен: https://xn--18-mlcyu7a.xn--p1ai. Нет query, якорей или дубля политики. CSS, JS и изображения в robots не заблокированы.

Дизайн, тексты страниц и структура интерфейса сохранены. React использует hydrateRoot; обработчики, формы, FAQ, меню и маршрутизация остаются в клиентском приложении.

## Проверки

- npm run build: PASS, exit 0. Vite предупреждает только о клиентском JS-бандле свыше 500 kB; ошибок сборки нет.
- npm run lint: PASS, 0 ошибок и предупреждений. Исправлены три исходных предупреждения без отключения правил: хук модального окна вынесен отдельно, удалён неиспользуемый экспорт serviceMenuLinks, корректно перечислены зависимости обработчика формы.
- npm run type-check: PASS, exit 0.
- HTTP без JavaScript: 18/18 URL возвращают 200, по одному H1, основные H2 и тексты, уникальные метаданные. Проверены локальные JS/CSS/favicon, внутренние ссылки и якоря.
- Apache 2.4.68 с итоговым .htaccess: 61 HTTP-проверка успешна; 18/18 страниц — 200; trailing slash, index.html и дубль политики — 301; неизвестные адреса, включая вложенный URL и отсутствующий asset, — настоящий 404.
- Chromium: все 18 страниц проверены с JavaScript и без него. Ошибок выполнения и гидратации нет; FAQ раскрывается/закрывается, модальное окно открывается и закрывается Escape, переход из подвала на другую страницу сбрасывает scrollY в 0; якоря продолжают работать. Прямое обновление внутренней страницы проверено.
- Это локальные проверки готовых файлов, не проверка развёрнутого сайта REG.RU: учётная запись хостинга не предоставлялась. Внешние изображения Readdy, шрифты Google и стили CDN сохранены, но проверить их невозможно из-за 403 сетевой политики среды. Недоступность из среды не доказывает недоступность из браузеров посетителей.

## Яндекс

Вебмастер: код подтверждения отсутствовал в архиве и не был предоставлен. Метрика: имеется только механизм подключения через VITE_PUBLIC_YANDEX_METRIKA_ID, номер отсутствует. В текущем ZIP активные коды отсутствуют. Поддержка подключения сохранена и исправлена: единый загрузчик с очередью ym; при заданных реальных параметрах сборка добавляет verification в head и один noscript-блок в body. Подробности в DEPLOYMENT.md.

## Изменённые исходные файлы

- index.html
- package.json
- public/robots.txt
- public/sitemap.xml
- src/App.tsx
- src/components/feature/MobileActionBar.tsx
- src/components/feature/RequestModal.tsx
- src/components/feature/ScrollManager.tsx
- src/components/feature/SiteHeader.tsx
- src/hooks/useDocumentMeta.ts
- src/hooks/useLeadForm.ts
- src/lib/analytics.ts
- src/main.tsx
- src/pages/home/components/ContactsSection.tsx
- src/pages/home/components/Hero.tsx
- src/pages/home/components/SituationBlock.tsx
- src/pages/info/page.tsx
- src/pages/service/page.tsx
- vite.config.ts

## Добавленные файлы

- .env.example
- .env.production
- .gitignore
- DEPLOYMENT.md
- package-lock.json
- public/.htaccess
- public/assets/favicon.svg
- scripts/apache-check.py
- scripts/browser-check.py
- scripts/build.mjs
- scripts/validate.py
- src/auto-imports.d.ts
- src/entry-static.tsx
- src/hooks/documentMetaContext.ts
- src/hooks/useRequestModal.ts

## Артефакты

- help18-regru-production.zip — только содержимое out/, без внешней папки, index.html в корне; .htaccess включён.
- help18-fixed-source.zip — исправленные исходники, lockfile, конфигурация домена, генератор и проверочные скрипты, без node_modules и out.
- DEPLOYMENT.md — инструкция загрузки и повторной сборки.
- validation-*.json — подробные результаты проверок.
