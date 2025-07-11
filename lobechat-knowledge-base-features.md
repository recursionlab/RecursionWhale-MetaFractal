# LobeChat Knowledge Base Features   
![logo.98482105](files\logo-98482105.png)    

Published on Friday, August 30 2024   
# LobeChat Knowledge Base Launch — From Now On, Every Step Counts   
The file upload and knowledge base features of LobeChat are officially launched! Bringing a brand new knowledge chat experience.   
![f2379de09d38fab21a4885dec1a75f18](files\f2379de09d38fab21a4885dec1a75f18.webp)    
As summer comes to a close, LobeChat reaches an important milestone after its 1.0 version — the official launch of the **file upload** and **knowledge base** features!   
 --- 
Since the open-source release of LobeChat, file upload and knowledge base have been highly anticipated features within the community. Since the release of version 1.0, about one-tenth of discussions and feedback in our forums have been related to these two features. The knowledge base has clearly become the most requested feature.   
![ed8e0e06b9a0984af7ff3e9619603e3e](files\ed8e0e06b9a0984af7ff3e9619603e3e.webp)    
Therefore, after releasing the server-side database solution, we immediately began developing the file upload and knowledge base features. After nearly two months of careful design and development, we are very pleased to announce that the LobeChat knowledge base is now officially available in both the **open-source version** and the **Cloud version**!   
## File Upload and Knowledge Base Management   
Compared to many similar products, we have been quite restrained in adding entry points in the sidebar. However, we believe that file upload and knowledge base management are very important primary functions. Therefore, we have added a prominent "Files" entry in the left sidebar of LobeChat. Through this new entry, you can easily access and manage various files stored in LobeChat.   
![2d9a6d96d8b33ceaef9545ce02f10d33](files\2d9a6d96d8b33ceaef9545ce02f10d33.webp)    
### Support for All File Types   
LobeChat now supports the upload of all types of files. Whether it's documents, images, audio, or video files, you can easily upload and store them. You can even use it **as a simple cloud storage without folder functionality**.   
![985de76e90ccd80efcc8e91667461bca](files\985de76e90ccd80efcc8e91667461bca.webp)    
Additionally, to ensure a basic user experience during file management, we provide essential file operation features such as multi-select, batch delete, and copy link, aimed at simplifying file management tasks and helping you efficiently handle a large number of files.   
![01d091e3eb89d95673855c314ce5981e](files\01d091e3eb89d95673855c314ce5981e.webp)    
### Online Preview of Various Common File Formats   
Currently, we support online previews of various common file formats such as PDF, Excel, Word, PPT, and TXT. You can directly view and review file contents without leaving the LobeChat interface.   
![2680a3e01497569b0ede92a3981fa195](files\2680a3e01497569b0ede92a3981fa195.webp)    
![393ee46820ddea10c954d687de80839c](files\393ee46820ddea10c954d687de80839c.webp)    
![665ddfb4420d9d7fb5fd4433268acaff](files\665ddfb4420d9d7fb5fd4433268acaff.webp)    
![369b8262a351769917ce822f8887c044](files\369b8262a351769917ce822f8887c044.webp)    
With countless file formats available, we understand that individual efforts cannot accommodate previews for all files. Therefore, we are eager to co-develop this feature with the community. Based on an open-source solution, we have developed an extensible file preview component, allowing the file preview functionality to easily support more file types beyond the currently supported formats.   
![d7b958ced65cbfe170705693976a0b55](files\d7b958ced65cbfe170705693976a0b55.webp)    
If the existing preview functionality in LobeChat does not meet your needs and you wish to support more file formats for preview, you can submit a PR ( [reference example](https://github.com/lobehub/lobe-chat/pull/3690)) or raise your request in the [discussion forum](https://github.com/lobehub/lobe-chat/discussions/3684), and we will implement features based on demand gradually.   
### Knowledge Base Management   
In the left menu of the interface, we have designed a clearly visible knowledge base management list that displays all created knowledge bases. Whether in the open-source version or the Cloud version, LobeChat allows you to create **an unlimited number** of knowledge bases.   
![4898d6ef75a834c5420d0616f7bc8ed3](files\4898d6ef75a834c5420d0616f7bc8ed3.webp)    
Currently, the first phase of the knowledge base temporarily supports file management. In the future, we will provide more advanced features such as recall testing, benchmark testing, and query configuration.   
![4cb8ea2cc8e8c9eb91d34644eed557a8](files\4cb8ea2cc8e8c9eb91d34644eed557a8.webp)    
![34d906bf90fa565e5af0e98d579373ac](files\34d906bf90fa565e5af0e98d579373ac.webp)    
## File Chunking and Embedding   
### One-Click Chunking and Embedding   
The biggest difference between our knowledge base and traditional cloud storage lies in our support for "chunking and embedding" of different types of files.   
![308a0c37aa715138e495839af19ece3b](files\308a0c37aa715138e495839af19ece3b.webp)    
Chunking is akin to splitting a file into text segments (Chunks) based on a specific structure, while embedding converts these corresponding text segments into a series of vectors like `[0.19,0.34,...]`, which are used for semantic matching queries in subsequent searches.   
It is this process of chunking and embedding that distinguishes us from traditional cloud storage, allowing us to truly enable chat with files/knowledge bases.   
### Chunking Detail Preview   
After completing the embedding, we also support previews of the vectorized segments. You can click to view the details of each chunk.   
![545b6f8708c138942b5fe25eb8aa0526](files\545b6f8708c138942b5fe25eb8aa0526.webp)    
Additionally, during the initial implementation, we explored a PDF chunking solution based on Unstructed.io. By combining the metadata from Unstructed chunking, we can even highlight the corresponding text area in the PDF when hovering over a chunk. This capability will also be introduced in future iterations.   
![3045506e79ceba4e47990e06bc5d03ca](files\3045506e79ceba4e47990e06bc5d03ca.webp)    
### Chunking Type Expansion   
Currently, the first phase of the knowledge base does not support embedding for audio, images, or video files, but our architectural design has laid the groundwork for supporting these file types in the future. By simply adding a new chunk type, we can achieve semantic search and chat for a category of files. For example, the file types we plan to support in the future include:   
- **Audio files like MP3**: Convert to text using STT models like `whisper-1`, and then proceed with chunking and embedding in pure text format;   
- **Image files**: Use models that support `vision`, such as `gpt-4o/sonnet`, to fully describe the content of images and save it as plain text, enabling subsequent chunking and embedding;   
- **Video files**: A simplified approach would be to extract audio and follow the audio file processing chain, but this would lose visual information. Ideally, we would use video understanding models for content extraction and then follow the text chunking approach;   
   
Like file previews, we understand that it is impossible for us to complete chunking solutions for all file types alone. Therefore, we hope to collaborate with the community to co-build the expansion of chunking types, gradually broadening the boundaries of knowledge base chat.   
## Knowledge Base Chat   
Finally, let's take a look at the core knowledge base chat feature.   
### Direct Upload in Chat   
We recognize that most users prefer to upload files directly in the chat box. Therefore, we have optimized the interaction design for file uploads, enhancing the preview and progress display features, allowing users to receive clearer feedback and a better experience during the upload process.   
![870e3d2a1f1f5e0c515d6b8ea61e300d](files\870e3d2a1f1f5e0c515d6b8ea61e300d.webp)    
Once the file upload is complete, the system will automatically perform chunking and embedding. This process is seamless, and once completed, you can immediately start a chat with the file, enabling in-depth exploration of its content and information retrieval.   
### File Preview and Recall Block Analysis   
Recently, we previewed the significance of Portal interaction, which can be very well integrated into the file chat scenario. You can click on a file to open and preview it directly in the Portal. The chat window provides an intuitive interface, allowing you to view the file content and the text blocks related to the current chat.   
![d0f123692dd4ee55431a73d51e463484](files\d0f123692dd4ee55431a73d51e463484.webp)    
By clicking on any text block, you can view its content in the Portal. This feature is very convenient for retrieval analysis.   
### Related Files/Knowledge Base Chat   
In designing the knowledge base chat feature, we ensured that it aligns with the logic of plugin usage. You can easily add selected knowledge bases or files to the chat assistant. Additionally, you can choose to activate or deactivate specific knowledge bases or files as needed.   
![ffb240f1304502d8b78e8dd3e15cbf04](files\ffb240f1304502d8b78e8dd3e15cbf04.webp)    
This design not only provides operational flexibility but also allows for switching between different knowledge bases based on specific application scenarios.   
![fe0261b081bf3dcdfc55ab22597ce0a6](files\fe0261b081bf3dcdfc55ab22597ce0a6.webp)    
## Conclusion Permalink for this section   
Unlike many AI chat products that focus on file uploads, our approach to this feature is more akin to "knowledge management." Therefore, we pay great attention to the content's ability to be retained in our design. In the future, we will also build on the existing capabilities to allow you to remember and retrieve previous chats, thereby "reinforcing the future." This is also the meaning behind our title, "A Journey of a Thousand Miles Begins with a Single Step."   
This concludes the knowledge base feature in LobeChat. We hope you enjoy it.   
LobeChat 1.0Server-side DatabaseKnowledge BaseFile UploadChatPDF   
Posted by   
![arvin-xu](files\arvin-xu.webp)    
Arvin Xu   
![canis-minor](files\canis-minor.webp)    
[Founder, Design Engineer](https://github.com/arvinxx)    
CanisMinor   
[Founder, Design Engineer](https://github.com/arvinxx)   
Share to   
[https://x.com/intent/tweet?hashtags=LobeChat%201.0%2CServer-side%20Database%2CKnowledge%20Base%2CFile%20Upload%2CChatPDF&text=LobeChat%20Knowledge%20Base%20Launch%20%E2%80%94%20From%20Now%20On%2C%20Every%20Step%20Counts%20-%20The%20file%20upload%20and%20knowledge%20base%20features%20of%20LobeChat%20are%20officially%20launched%21%20Bringing%20a%20brand%20new%20knowledge%20chat%20experience.&url=https%3A%2F%2Flobehub.com%2Fblog%2Fknowledge-base](https://x.com/intent/tweet?hashtags=LobeChat 1.0,Server-side Database,Knowledge Base,File Upload,ChatPDF&text=LobeChat Knowledge Base Launch — From Now On, Every Step Counts - The file upload and knowledge base features of LobeChat are officially launched! Bringing a brand new knowledge chat experience.&url=https://lobehub.com/blog/knowledge-base) [https://www.reddit.com/submit?title=LobeChat%20Knowledge%20Base%20Launch%20%E2%80%94%20From%20Now%20On%2C%20Every%20Step%20Counts%20-%20The%20file%20upload%20and%20knowledge%20base%20features%20of%20LobeChat%20are%20officially%20launched%21%20Bringing%20a%20brand%20new%20knowledge%20chat%20experience.%20%23lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf&url=https%3A%2F%2Flobehub.com%2Fblog%2Fknowledge-base](https://www.reddit.com/submit?title=LobeChat Knowledge Base Launch — From Now On, Every Step Counts - The file upload and knowledge base features of LobeChat are officially launched! Bringing a brand new knowledge chat experience. #lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf&url=https://lobehub.com/blog/knowledge-base) [https://t.me/share/url%22?text=LobeChat%20Knowledge%20Base%20Launch%20%E2%80%94%20From%20Now%20On%2C%20Every%20Step%20Counts%20-%20The%20file%20upload%20and%20knowledge%20base%20features%20of%20LobeChat%20are%20officially%20launched%21%20Bringing%20a%20brand%20new%20knowledge%20chat%20experience.%20%23lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf&url=https%3A%2F%2Flobehub.com%2Fblog%2Fknowledge-base](https://t.me/share/url%22?text=LobeChat Knowledge Base Launch — From Now On, Every Step Counts - The file upload and knowledge base features of LobeChat are officially launched! Bringing a brand new knowledge chat experience. #lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf&url=https://lobehub.com/blog/knowledge-base) [https://api.whatsapp.com/send?text=LobeChat%20Knowledge%20Base%20Launch%20%E2%80%94%20From%20Now%20On%2C%20Every%20Step%20Counts%20-%20The%20file%20upload%20and%20knowledge%20base%20features%20of%20LobeChat%20are%20officially%20launched%21%20Bringing%20a%20brand%20new%20knowledge%20chat%20experience.%20https%3A%2F%2Flobehub.com%2Fblog%2Fknowledge-base%20%23lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf](https://api.whatsapp.com/send?text=LobeChat Knowledge Base Launch — From Now On, Every Step Counts - The file upload and knowledge base features of LobeChat are officially launched! Bringing a brand new knowledge chat experience. https://lobehub.com/blog/knowledge-base #lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf) [https://mastodon.social/share?text=LobeChat%20Knowledge%20Base%20Launch%20%E2%80%94%20From%20Now%20On%2C%20Every%20Step%20Counts%20-%20The%20file%20upload%20and%20knowledge%20base%20features%20of%20LobeChat%20are%20officially%20launched%21%20Bringing%20a%20brand%20new%20knowledge%20chat%20experience.%20%23lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf&url=https%3A%2F%2Flobehub.com%2Fblog%2Fknowledge-base](https://mastodon.social/share?text=LobeChat Knowledge Base Launch — From Now On, Every Step Counts - The file upload and knowledge base features of LobeChat are officially launched! Bringing a brand new knowledge chat experience. #lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf&url=https://lobehub.com/blog/knowledge-base) [https://www.linkedin.com/sharing/share-offsite/?url=https%3A%2F%2Flobehub.com%2Fblog%2Fknowledge-base](https://www.linkedin.com/sharing/share-offsite/?url=https://lobehub.com/blog/knowledge-base) [http://service.weibo.com/share/share.php?sharesource=weibo&title=LobeChat%20Knowledge%20Base%20Launch%20%E2%80%94%20From%20Now%20On%2C%20Every%20Step%20Counts%20-%20The%20file%20upload%20and%20knowledge%20base%20features%20of%20LobeChat%20are%20officially%20launched%21%20Bringing%20a%20brand%20new%20knowledge%20chat%20experience.%20%23lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf&url=https%3A%2F%2Flobehub.com%2Fblog%2Fknowledge-base](http://service.weibo.com/share/share.php?sharesource=weibo&title=LobeChat Knowledge Base Launch — From Now On, Every Step Counts - The file upload and knowledge base features of LobeChat are officially launched! Bringing a brand new knowledge chat experience. #lobeChat10%20%23serverSideDatabase%20%23knowledgeBase%20%23fileUpload%20%23chatPdf&url=https://lobehub.com/blog/knowledge-base)   
Related Reading   
[Build Your Own ChatPDF in 10 Minutes](https://lobehub.com/blog/build-your-own-chatpdf-in-10-minutes)   
![tc](files\tc.webp)    
Sep 06, 2024   
[5 Recommended Excellent AI Knowledge Bases: Intelligent Assistants to Enhance Efficiency](https://lobehub.com/blog/5-recommended-knowledge-base-ai-chatbot-tools)   
![tc](files\tc.webp)    
Aug 29, 2024   
On This Page   
[File Upload and Knowledge Base Management](https://lobehub.com/blog/knowledge-base#file-upload-and-knowledge-base-management)   
[Support for All File Types](https://lobehub.com/blog/knowledge-base#support-for-all-file-types)   
[Online Preview of Various Common File Formats](https://lobehub.com/blog/knowledge-base#online-preview-of-various-common-file-formats)   
[Knowledge Base Management](https://lobehub.com/blog/knowledge-base#knowledge-base-management)   
[File Chunking and Embedding](https://lobehub.com/blog/knowledge-base#file-chunking-and-embedding)   
[One-Click Chunking and Embedding](https://lobehub.com/blog/knowledge-base#one-click-chunking-and-embedding)   
[Chunking Detail Preview](https://lobehub.com/blog/knowledge-base#chunking-detail-preview)   
[Chunking Type Expansion](https://lobehub.com/blog/knowledge-base#chunking-type-expansion)   
[Knowledge Base Chat](https://lobehub.com/blog/knowledge-base#knowledge-base-chat)   
[Direct Upload in Chat](https://lobehub.com/blog/knowledge-base#direct-upload-in-chat)   
[File Preview and Recall Block Analysis](https://lobehub.com/blog/knowledge-base#file-preview-and-recall-block-analysis)   
[Related Files/Knowledge Base Chat](https://lobehub.com/blog/knowledge-base#related-files-knowledge-base-chat)   
[Conclusion](https://lobehub.com/blog/knowledge-base#conclusion)   
Launch Self-Efficacy. Rediscover Passion of Creation.   
Explore the rich ecosystem of intelligent agents and easily orchestrate your ideal workflow, you’ll do amazing things with LobeChat.   
[Free Trial](https://lobechat.com/?utm_source=landing&utm_content=global_footer_cta) [GitHub](https://github.com/lobehub/lobe-chat)   
![logo-3d](files\logo-3d.webp)    
Empowering your AI dreams   
© 2023-2025 LobeHub, LLC   
[https://github.com/lobehub/lobe-chat](https://github.com/lobehub/lobe-chat) [https://x.com/lobehub](https://x.com/lobehub) [https://discord.gg/AYFPHvv2jT](https://discord.gg/AYFPHvv2jT) [https://medium.com/@lobehub](https://medium.com/@lobehub)   
Product   
[What’s New](https://github.com/lobehub/lobe-chat/blob/main/CHANGELOG.md) [Pricing and Plans](https://lobehub.com/pricing) [Assistant Market](https://lobehub.com/assistants) [Plugin Market](https://lobehub.com/plugins) [Community Edition](https://github.com/lobehub/lobe-chat)   
Features   
[Features Overview](https://lobehub.com/features) [LobeChat vs. ChatGPT](https://lobehub.com/blog/lobechat-vs-chatgpt-plus) [LobeChat vs. Poe](https://lobehub.com/blog/lobechat-vs-poe)   
Resources   
[Blog](https://lobehub.com/blog) [AI / LLM Icons](https://lobehub.com/icons) [Quick Start](https://lobehub.com/docs) [Developer Handoff](https://github.com/lobehub/lobe-chat/wiki) [Feedback](https://github.com/lobehub/lobe-chat/issues)   
Open Source   
[🤯 Lobe Chat ](https://github.com/lobehub/lobe-chat)🅰[️ Lobe Theme 🌏](https://github.com/lobehub/sd-webui-lobe-theme) [Lobe i18n 🍭 ](https://github.com/lobehub/lobe-cli-toolbox/tree/master/packages/lobe-i18n)L[obe UI 🥨 L](https://github.com/lobehub/lobe-ui)o[be Icons 🎤 Lo](https://github.com/lobehub/lobe-icons)b[e TTS   
Company   
[About Us](https://github.com/lobehub) [Terms of Service](https://lobehub.com/terms) [Privacy Policy](https://lobehub.com/privacy) [Contact](mailto:hello@lobehub.com)   
