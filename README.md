
 #Unity 4.x and 5.x edition UGUI InputField bug Fix
 
 download “MyInputField.cs” file and put it in your Unity script directory. Use MyInputField to replace InputFiled everywhere in your game, then you can avoid the bug. 
 
#Unity 多个版本的InputField 控件都存在越界bug。

仅需下载MyInputField.cs，游戏中以后都使用此控件,就能避免Unity的InputField 越界bug。（已在源码基础上改动代码，绕过了该bug。）

bug原因：
cachedInputTextGenerator.Populate(processed, settings); （TextGenerator.Populate）内部有bug，执行后偶尔出现 cachedInputTextGenerator.lineCount == 3,但cachedInputTextGenerator.lines.Count == 0，造成DetermineCharacterLine返回-1， 使得数组越界。

解决方法：
1、添加如下代码
                    if (cachedInputTextGenerator.lineCount != cachedInputTextGenerator.lines.Count)
                    {
                        //Populate函数偶然会出bug，造成lineCount ！= lines.Count。若能再执行一次Populate，数据就会正常
                        //但Populate函数的参数必须与上次传入的不同才会重新执行，所以此处删除最后一个字。
                        if (processed.Length > 0)
                        {
                            var lastChar = processed[processed.Length - 1];
                            processed = processed.Remove(processed.Length - 1);
                            cachedInputTextGenerator.Populate(processed, settings);
                            processed += lastChar;
                        }
                    }
                    cachedInputTextGenerator.Populate(processed, settings); 

2、在越界前height -= lines[endLine].height;判断-1，做保护

