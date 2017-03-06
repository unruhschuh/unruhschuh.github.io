---
layout: post
title:  "Matlab: Open current document in MacVim / GVim"
date:   2017-03-05 22:15:31 +0100
categories: matlab
---
I tried many times to replace the default editor of Matlab with vim, but there are too many things, that are simply great about the Matlab IDE, namely
 * [code refactoring](http://blogs.mathworks.com/videos/2014/01/31/refactor-code-to-rename-variable-with-with-matlab-editor/){:target="_blank"}
 * [debugging](https://de.mathworks.com/help/matlab/matlab_prog/debugging-process-and-features.html){:target="_blank"}
 * [profiling](https://de.mathworks.com/help/matlab/matlab_prog/profiling-for-improving-performance.html){:target="_blank"}

The one feature of vim I miss the most, in almost any editor, is search / replace using regular expressions (well, that and navigating with h-j-k-l).

For these reasons, I wrote a little script, which will open the current document -- at the current line -- in either MacVim or GVim, depending on which OS I am running.
Of course you need to have MacVim or GVim installed on your system for this to work.
On a Mac, you also need to put the shell script `mvim`, which comes with the installer of MacVim, in `YOUR_HOME/bin/mvim`, as described in the [MacVim FAQ](https://github.com/macvim-dev/macvim/wiki/FAQ#how-can-i-open-files-from-terminal){:target="_blank"}.

Now save the script below as `open_in_vim.m` in a directory, which is included in your Matlab's [path variable](https://de.mathworks.com/help/matlab/ref/path.html){:target="_blank"}.
Any time you want to open the current document in Vim, simply type `open_in_vim` in the command window.
To make this new functionality more accessible, you can create a shortcut in [Matlab's toolbar](https://de.mathworks.com/help/matlab/matlab_env/create-matlab-shortcuts-to-rerun-commands.html){:target="_blank"}.

<script src="https://gist.github.com/unruhschuh/2becfe1bc1705ae168a3d6f38fc33080.js"></script>

<!--
{% highlight matlab %}
function open_in_vim
    os = strsplit(system_dependent('getos'), ' ');
    os = os(1);
    
    edhandle     = com.mathworks.mlservices.MLEditorServices.getEditorApplication.getActiveEditor;
    storageClass = edhandle.getStorageLocation.getClass;
    lineNumber   = edhandle.getLineNumber + 1; % weirdly enough, matlab starts counting line numbers at 0
    
    if isequal(storageClass.toString.toCharArray.', 'class com.mathworks.widgets.datamodel.FutureFileStorageLocation')
        disp('Error: The file has not been saved yet.')
    elseif isequal(storageClass.toString.toCharArray.', 'class com.mathworks.widgets.datamodel.FileStorageLocation')
        filename = edhandle.getLongName.toString.toCharArray.';
        if strcmp(os, 'Linux')
            system(['/usr/bin/gvim +', num2str(lineNumber), ' "', filename, '"']);
        elseif strcmp(os, 'Darwin')
            system(['~/bin/mvim +', num2str(lineNumber), ' "', filename, '"']);
        end
    end
end
{% endhighlight %}
-->

