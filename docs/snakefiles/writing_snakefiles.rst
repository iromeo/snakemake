.. _user_manual-writing_snakefiles:

=================
Writing Workflows
=================

In Snakemake, workflows are specified as Snakefiles.
Inspired by GNU Make, a Snakefile contains rules that denote how to create output files from input files.
Dependencies between rules are handled implicitly, by matching filenames of input files against output files.
Thereby wildcards can be used to write general rules.

.. _snakefiles-grammar:

-------
Grammar
-------

The Snakefile syntax obeys the following grammar, given in extended Backus-Naur form (EBNF)

.. code-block:: text

    snakemake    = statement | rule | include | workdir | module | configfile | container
    rule         = "rule" (identifier | "") ":" ruleparams
    include      = "include:" stringliteral
    workdir      = "workdir:" stringliteral
    module       = "module" identifier ":" moduleparams
    configfile   = "configfile" ":" stringliteral
    userule      = "use" "rule" (identifier | "*") "from" identifier ["as" identifier] ["with" ":" norunparams]
    ni           = NEWLINE INDENT
    norunparams  = [ni input] [ni output] [ni params] [ni message] [ni threads] [ni resources] [ni log] [ni conda] [ni container] [ni benchmark] [ni cache]
    ruleparams   = norunparams [ni (run | shell | script | notebook)] NEWLINE snakemake
    input        = "input" ":" parameter_list
    output       = "output" ":" parameter_list
    params       = "params" ":" parameter_list
    log          = "log" ":" parameter_list
    benchmark    = "benchmark" ":" statement
    cache        = "cache" ":" bool
    message      = "message" ":" stringliteral
    threads      = "threads" ":" integer
    resources    = "resources" ":" parameter_list
    version      = "version" ":" statement
    conda        = "conda" ":" stringliteral
    container    = "container" ":" stringliteral
    run          = "run" ":" ni statement
    shell        = "shell" ":" stringliteral
    script       = "script" ":" stringliteral
    notebook     = "notebook" ":" stringliteral
    moduleparams = [ni snakefile] [ni metawrapper] [ni config] [ni skipval]
    snakefile    = "snakefile" ":" stringliteral
    metawrapper  = "meta_wrapper" ":" stringliteral
    config       = "config" ":" stringliteral
    skipval      = "skip_validation" ":" stringliteral
    

while all not defined non-terminals map to their Python equivalents.

.. _snakefiles-depend_version:

Depend on a Minimum Snakemake Version
-------------------------------------

From Snakemake 3.2 on, if your workflow depends on a minimum Snakemake version, you can easily ensure that at least this version is installed via

.. code-block:: python

    from snakemake.utils import min_version

    min_version("3.2")

given that your minimum required version of Snakemake is 3.2. The statement will raise a WorkflowError (and therefore abort the workflow execution) if the version is not met.


.. _snakefiles-best_practices:

--------------
Best practices
--------------

Snakemake (>=5.11) comes with a code quality checker (a so called linter), that analyzes your workflow and highlights issues that should be solved in order to follow best practices, achieve maximum readability, and reproducibility.
The linter can be invoked with 

.. code-block:: bash

    snakemake --lint

given that a ``Snakefile`` or ``workflow/Snakefile`` is accessible from your working directory.
It is **highly recommended** to run the linter before publishing any workflow, asking questions on Stack Overflow or filing issues on Github.

Further, there is an automatic formatter for Snakemake workflows, called `Snakefmt <https://github.com/snakemake/snakefmt>`_, which should be applied to any Snakemake workflow before publishing it.

When publishing your workflow in a `Github <https://github.com>`_ repository, it is a good idea to add some minimal test data and configure `Github Actions <https://github.com/features/actions>`_ for continuously testing the workflow on each new commit.
For this purpose, we provide predefined Github actions for both running tests and linting `here <https://github.com/snakemake/snakemake-github-action>`_, as well as formatting `here <https://github.com/snakemake/snakefmt#github-actions>`_.

For publishing and distributing a Snakemake workflow, it is a good idea to stick to a :ref:`standardized structure <distribution_and_reproducibility>` that is expected by frequent users of Snakemake.
Finally, the `Snakemake workflow catalog <https://snakemake.github.io/snakemake-workflow-catalog>`_ automatically lists Snakemake workflows hosted on `Github <https://github.com>`_ if they follow certain `rules <https://snakemake.github.io/snakemake-workflow-catalog/?rules=true>`_.
By complying to these `rules <https://snakemake.github.io/snakemake-workflow-catalog/?rules=true>`_ you can make your workflow more discoverable and even automate its usage documentation (see `"Standardized usage" <https://snakemake.github.io/snakemake-workflow-catalog/?rules=true>`_).