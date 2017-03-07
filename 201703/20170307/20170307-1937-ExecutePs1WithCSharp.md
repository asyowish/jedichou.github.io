### 20170302 - 使用C#执行PowerShell脚本

可以建立个命令行项目，然后运行以下内容（[参考网址](https://www.baidu.com/link?url=jdcLwKgbsyc4rgf5-IFMBlcpPsxI26Uu8vdR11MtahH_Dia5U_ki3hf76kz7w4fWUHNmuKQToMD7finy8Lfo4a&wd=&eqid=956b7e270058ce270000000258be999b)）

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Management;
using System.Management.Automation;
using System.Management.Automation.Runspaces;
using System.Collections.ObjectModel;

namespace CSharpExecutePs1
{
    class Program
    {
        static void Main(string[] args)
        {
            string script = @"Get-ChildItem c:\";
            string r = RunScript(script);
            Console.WriteLine(r);
            Console.WriteLine("End");
            Console.Read();
        }
        
        private static string RunScript(string scriptText)
        {
            // create Powershell runspace
            Runspace runspace = RunspaceFactory.CreateRunspace();
            // open it
            runspace.Open();
            // create a pipeline and feed it the script text
            Pipeline pipeline = runspace.CreatePipeline();
            pipeline.Commands.AddScript(scriptText);
            pipeline.Commands.Add("Out-String");

            // execute the script
            Collection<PSObject> results = pipeline.Invoke();
            // close the runspace
            runspace.Close();

            // convert the script result into a single string
            StringBuilder stringBuilder = new StringBuilder();
            foreach (PSObject obj in results)
            {
                stringBuilder.AppendLine(obj.ToString());
            }
            return stringBuilder.ToString();
        }

        public void RunScript(List<string> scripts)
        {
            try
            {
                Runspace runspace = RunspaceFactory.CreateRunspace();
                runspace.Open();
                Pipeline pipeline = runspace.CreatePipeline();
                foreach (var scr in scripts)
                {
                    pipeline.Commands.AddScript(scr);
                }

                //返回结果   
                var results = pipeline.Invoke();
                runspace.Close();

            }
            catch (Exception e)
            {
                Console.WriteLine(DateTime.Now.ToString() + "日志记录:执行ps命令异常：" + e.Message);
            }
        }
    }
}

```

另外注意：如果你是在WIN7上，则要加载System.Management.Automation.dll。这个文件有存放多个地址，你要加载的是“C:\Program Files (x86)\Reference Assemblies\Microsoft\WindowsPowerShell\v1.0”；其他地方的我也试过都无法编译！